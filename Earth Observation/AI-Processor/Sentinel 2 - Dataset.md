```
https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Collection/Name eq 'SENTINEL-2' and OData.CSC.Intersects(area=geography'SRID=4326;POLYGON((3.2833 45.3833, 11.2 45.3833, 11.2 50.1833, 3.2833 50.1833, 3.2833 45.3833))') and (contains(Name,'MSIL1C') or contains(Name,'MSIL2A')) and ContentDate/Start gt 2025-01-01T00:00:00.000Z and ContentDate/Start lt 2025-01-15T23:59:59.999Z and Attributes/OData.CSC.DoubleAttribute/any(att:att/Name eq 'cloudCover' and att/OData.CSC.DoubleAttribute/Value le 100)&$orderby=ContentDate/Start&$top=100&$count=true

```

V0 

bbox = [3.2833, 45.3833, 11.2, 50.1833]
start_date = datetime(2024, 1, 1)
end_date = datetime(2025, 1, 1)
cloud_cover = 100 


V1 
bbox = [3.2833, 45.3833, 11.2, 50.1833]
start_date = datetime(2020, 1, 1)
end_date = datetime(2025, 1, 1)
max_items = 1000
max_cloud_cover = 10




```python
# Import necessary libraries
import torch
import numpy as np
import matplotlib.pyplot as plt
import cv2
# Get the first batch from train_loader
for x_data, y_data in train_loader:
	# Print tensor information
	print(f"Batch shape: {x_data.shape}")
	print(f"Data type: {x_data.dtype}")
	print(f"Value range: min={x_data.min().item()}, max={x_data.max().item()}")
	# Take the first image from the batch
	x_sample = x_data[0] # Shape should be [C, H, W]
	# # Reverse the transforms
	# # Step 1: Convert back from CHW to HWC format
	x_data_hwc = x_sample.permute(1, 2, 0)
	print(x_data_hwc.shape)
	# # Step 2: Convert from tensor to numpy array
	x_data_np = x_data_hwc.detach().cpu().numpy()
	# # Step 3: Reverse the normalization (multiply by 255)
	x_data_np = x_data_np * 255.0
	# # Step 4: Convert back to uint8 (original image format)
	x_data_original = x_data_np.astype(np.uint8)
	# Visualize the original image
	plt.figure(figsize=(10, 10))
	plt.imshow(x_data_original)
	plt.title("Reconstructed Original Image")
	plt.axis('off')
	plt.show()
```

The V1 version dataset 


Dataset V2 issues: 

test index 75 


