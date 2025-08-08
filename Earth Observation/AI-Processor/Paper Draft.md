




We implement a comprehensive pipeline for training and evaluating deep learning models on multi-spectral satellite imagery, designed to robustly handle invalid pixels and assess reconstruction fidelity across spectral channels. Each spectral band is normalized individually via percentile stretching (2nd–98th percentiles) and a binary validity mask is computed to exclude invalid or missing data (Outside of datastrip acquistion). These masks are propagated through all stages of the pipeline to ensure that learning and evaluation are restricted to meaningful data.

Input (`x`) and target (`y`) images are read as multi-band stacks, resized to a common spatial resolution, and converted into PyTorch tensors. ~~A final validity mask is computed as the logical intersection of input and target masks to identify pixels valid in both domains.~~

For evaluation, we propose a band-wise metric framework that supports per-channel computation of standard and remote sensing-specific metrics, including Peak Signal-to-Noise Ratio (PSNR), Root Mean Square Error (RMSE), Structural Similarity Index (SSIM), and Spectral Angular Mapper (SAM). All metrics are computed using only the valid pixels, ensuring robustness to incomplete or corrupted data.

During training, loss is computed only over valid pixels using the precomputed masks, enabling the model to focus on high-confidence regions. This design allows for stable learning from real-world satellite data, which often includes artifacts, missing pixels, and heterogeneous spectral responses.