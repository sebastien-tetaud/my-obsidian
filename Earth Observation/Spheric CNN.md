


## **RegionalSphericalConv.**init**()**

This is the constructor that sets up the spherical convolution layer for your regional HEALPix data. Unlike the original implementation that assumes a full sphere, this version works with only the HEALPix cells present in the dataset. It takes `available_cell_ids` (the cells that actually contain data in your geographic region) and the HEALPix resolution level. The constructor builds the neighbor relationships, creates efficient lookup tables, and initializes the underlying 1D convolution layer that will process the 3×3 patches. The key innovation here is that it adapts to your specific data coverage rather than assuming global coverage.

## **_build_neighbor_index()**

This function implements a strategy for creating 3×3 patches on the sphere. For each available cell in your dataset (optionally subsampled by stride), it uses HEALPix's `get_all_neighbours()` to find the 8 surrounding cells. However, since the data only covers a specific region, some of these neighbors might not exist in your dataset. The function handles this by replacing any missing neighbors with the center cell ID itself - effectively implementing "same padding" at the boundaries. This ensures every patch has exactly 9 elements (center + 8 neighbors), making it compatible with standard CNN operations while preserving the spherical topology.

## **_convert_to_data_indices()**

This function performs a crucial optimization for computational efficiency. While `_build_neighbor_index()` works with HEALPix cell IDs (which can be large, sparse numbers), your actual data array uses sequential indices from 0 to N-1. This function creates a mapping that converts the HEALPix cell IDs in each patch to the corresponding positions in your data array. This pre-computation means that during the forward pass, you can directly index into your data tensor without expensive lookups, making the convolution much faster.

## **forward()**

This is where the actual spherical convolution happens. The function takes the input tensor of shape [batch, channels, cells] and extracts all the 3×3 patches simultaneously using advanced indexing. It reshapes these patches into a format suitable for PyTorch's 1D convolution (which treats each 9-element patch as a "sequence"), applies the learned convolutional weights, and returns the feature maps. The beauty of this approach is that it maintains the spherical neighborhood relationships while leveraging standard CNN operations, allowing you to use existing deep learning frameworks efficiently.

## **example_with_your_data()**

This comprehensive example demonstrates the complete workflow with your actual HEALPix data. It starts by extracting all spectral bands from your `ds_healpix` dataset, stacks them into a multi-channel tensor (just like RGB channels in regular images), creates the spherical convolution layer, and performs a forward pass. The example shows how your satellite data with multiple spectral bands gets transformed into feature maps that respect the spherical geometry. It also includes practical details like tensor shapes and data type conversions needed for PyTorch.

## **example_with_stride()**

This function illustrates how the stride parameter affects computational efficiency and spatial resolution. By using different stride values (1, 2, 4, 8), you can control how many patches are generated - stride=1 creates a patch for every available cell, while stride=4 creates patches for every 4th cell, reducing computation by 4×. This is particularly useful for multi-scale processing or when you need to balance computational resources with spatial detail. The function shows the trade-off between spatial resolution and computational efficiency.

The key advantage of this approach is that it seamlessly integrates your regional HEALPix strategy with standard deep learning, allowing you to build sophisticated spherical CNNs that work efficiently on your satellite data while respecting the underlying spherical geometry of Earth's surface.



####


## **Detailed Function Explanations**

### **1. NeighborIndexProcessor Class**

This is the core innovation that separates geometric processing from the neural network. The class handles all HEALPix-specific computations outside the model, making it reusable across different data chunks. It takes a HEALPix level (19 in my case) and nest parameter, then provides methods to compute neighbor relationships for any set of cell IDs. The processor includes intelligent caching to avoid recomputing identical neighbor patterns, which is crucial when processing multiple similar chunks. The key insight here is that neighbor relationships depend only on the cell IDs and geometric properties, not on the actual data values, so this can be pre-computed and cached.

### **2. build_neighbor_indices() Method**

This function implements your original neighbor-finding strategy but in a flexible, external manner. For each chunk's available cell IDs, it creates a mapping from HEALPix cell IDs to data array indices (0, 1, 2, ..., N-1). Then, for each center cell (optionally subsampled by stride), it uses HEALPix's `get_all_neighbours()` to find the 8 surrounding cells. If a neighbor is missing (edge of region) or invalid (-1 from HEALPix), it replaces it with the center cell ID, implementing "same padding." The result is a tensor of shape [N_patches, 9] where each row contains data indices for a 3×3 patch centered on each cell.

### **3. SphericalConv Class (Updated)**

The fundamental change here is that this class no longer stores any cell-specific information. Instead of building neighbor indices in the constructor, it now receives pre-computed neighbor indices during the forward pass. The class only contains the Conv1d layer and weight initialization. During forward pass, it takes the input tensor and neighbor indices, extracts 3×3 patches using advanced indexing (`x[:, :, neighbor_indices]`), reshapes them into a format suitable for 1D convolution, and applies the learned convolution weights. This design makes the layer completely agnostic to which specific HEALPix cells it's processing.

### **4. SphericalConvBlock Class**

This wrapper combines the spherical convolution with batch normalization and ReLU activation, following standard deep learning practices. The key modification is that it now passes the neighbor indices through to the underlying SphericalConv layer. Batch normalization helps stabilize training by normalizing the feature distributions, while ReLU introduces non-linearity. The block pattern (Conv → BatchNorm → ReLU) is a proven architectural choice that promotes stable gradient flow and faster convergence during training.

### **5. SphericalDoubleConvBlock Class**

This implements the classic "double convolution" pattern common in U-Net architectures, where two convolution blocks are applied sequentially. The first block can optionally use a stride for downsampling, while the second always uses stride=1 to refine features. This double application allows the network to learn more complex feature representations at each level. The sequential application means features pass through: Conv1 → BN → ReLU → Conv2 → BN → ReLU, creating a deeper representation within each processing stage.

### **6. Model Class (Flexible)**

The top-level model class now has no knowledge of specific cell configurations, making it truly reusable across different data chunks. It simply wraps the SphericalDoubleConvBlock and forwards both the input data and neighbor indices. This design philosophy separation of concerns - the model handles the neural computation while external processors handle the geometric relationships. The model can now be trained once and applied to any chunk with any cell configuration, as long as the neighbor indices are provided.

### **7. process_chunk() Function**

This orchestrates the entire processing pipeline for a single data chunk. It loads the zarr file, extracts cell IDs and spectral data, computes neighbor indices using the external processor, prepares PyTorch tensors, and runs the forward pass. The function is designed to be called repeatedly for different chunks while reusing the same model and neighbor processor. It handles all the data movement between CPU and GPU, tensor formatting, and error handling. The output includes both the processed features and the cell IDs for proper result aggregation.

### **8. clear_memory() Function**

This function performs comprehensive memory cleanup essential for processing large datasets. It deletes specific variables from the global namespace, forces Python garbage collection to free unused memory, and most importantly, clears the GPU cache using `torch.cuda.empty_cache()`. The GPU synchronization ensures all pending operations complete before cleanup. This is crucial when processing multiple large chunks sequentially, as GPU memory can accumulate and cause out-of-memory errors. The function provides feedback on successful cleanup.

### **Pipeline Benefits**

The separation of geometric processing (neighbor indices) from neural computation (model) provides several key advantages: the model becomes reusable across different chunks, memory usage is more predictable, the neighbor computation can be cached and optimized separately, and the architecture supports parallel processing of multiple chunks. This design also makes the code more maintainable and testable, as each component has a single, well-defined responsibility.