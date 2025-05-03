[[Deep Learning]]
In the context of [[Super Resolution]]: 

```Python

class CreateDataset(Dataset):

def __init__(self, hr_data, lr_data, hr_mean, hr_std, lr_mean, lr_std):

"""

Args:

hr_data (xarray.DataArray): High-resolution data

lr_data (xarray.DataArray): Low-resolution data

"""

self.hr_data = hr_data

self.lr_data = lr_data

self.hr_mean, self.hr_std = hr_mean, hr_std

self.lr_mean, self.lr_std = lr_mean, lr_std

  

# Define the transformations for high-resolution and low-resolution data

self.hr_transform = transforms.Normalize(mean=[hr_mean], std=[hr_std])

self.lr_transform = transforms.Normalize(mean=[lr_mean], std=[lr_std])

  

def __len__(self):

# Dataset length is the number of time steps

return self.hr_data.shape[0]

  

def __getitem__(self, idx):

"""

Args:

idx (int): Index of the sample to return

Returns:

tuple: (low-resolution, high-resolution) data as tensors

"""

  

# Select the time slice at the given index

hr_sample = self.hr_data.isel(time=idx).values # (lat, lon)

lr_sample = self.lr_data.isel(time=idx).values # (lat, lon)

# Convert to torch tensors and reshape to match (C, H, W) format

hr_sample = torch.tensor(hr_sample, dtype=torch.float32).unsqueeze(0)

lr_sample = torch.tensor(lr_sample, dtype=torch.float32).unsqueeze(0)

  

hr_sample = self.hr_transform(hr_sample)

lr_sample = self.lr_transform(lr_sample)

  

return lr_sample, hr_sample
```


```Python

# Compute mean and standard deviation for normalization

hr_mean, hr_std = compute_mean_std(hr)

lr_mean, lr_std = compute_mean_std(lr)

# Update config with computed mean and std

config["dataset"]["hr_mean"] = hr_mean

config["dataset"]["hr_std"] = hr_std

config["dataset"]["lr_mean"] = lr_mean

config["dataset"]["lr_std"] = lr_std

```


✅ **Yes, it is correct if**:

- Normalize the entire dataset to have a consistent scale.
- The computed `hr_mean`, `hr_std`, `lr_mean`, and `lr_std` are **precomputed** from all the data before training.
- You ensure that the statistics are the same for training and testing datasets (i.e., you compute them only from the training dataset and apply them to the test dataset to avoid data leakage).

❌ **Potential issues**:

1. **Data Leakage in Cross-Validation**
    
    - If you compute the mean and std **from the entire dataset including validation/test data**, it can introduce **data leakage**.
    - Instead, compute `mean` and `std` **only on the training set** and use those values for validation and testing.
2. **Temporal Dataset Considerations**
    
    - Since climate data has temporal dependencies, if your dataset covers different time periods (e.g., training: 1970-2000, testing: 2001-2020), make sure that the computed mean/std are not biased due to different climate regimes.
3. **Spatial Variability**
    
    - Climate data often has strong **spatial variability** (e.g., land vs. ocean, polar vs. equatorial). A **global mean** might not capture local differences well.
    - Alternative: Normalize each **spatial location** separately or apply a **local normalization technique**.

### 🌍**Alternative Approaches**

- **Instance Normalization:** Normalize each sample independently (i.e., compute mean/std per image).
- **Spatial Normalization:** Normalize each grid point (lat/lon) using **mean and std across time**.
- **Batch Normalization:** Let the model learn the normalization dynamically.



## Aproach


```Python

import numpy as np

from torch.utils.data import Dataset, DataLoader

from torchvision import transforms

import torch

  
  

def z_score_normalization(tensor: torch.Tensor) -> torch.Tensor:

"""

Perform Z-score normalization for each channel of the input tensor.

  

Args:

tensor (torch.Tensor): Input tensor of shape [C, H, W].

  

Returns:

torch.Tensor: Normalized tensor.

"""

mean = tensor.mean(dim=[1, 2], keepdim=True) # Mean along H and W for each channel

std = tensor.std(dim=[1, 2], keepdim=True) # Standard deviation along H and W for each channel

  

# Prevent division by zero

std = std + 1e-6

  

return (tensor - mean) / std

  
  

class CreateDataset(Dataset):

"""

PyTorch Dataset for handling low-resolution (LR) and high-resolution (HR)

climate data for super-resolution tasks.

  

Attributes:

hr_data (xarray.Dataset): High-resolution dataset.

lr_data (xarray.Dataset): Low-resolution dataset.

hr_variables (list): List of HR variable names.

lr_variables (list): List of LR variable names.

"""

  

def __init__(self, lr_data, hr_data):

"""

Initialize the dataset with low-resolution and high-resolution climate data.

  

Args:

lr_data (xarray.Dataset): Low-resolution dataset.

hr_data (xarray.Dataset): High-resolution dataset.

"""

self.hr_data = hr_data

self.lr_data = lr_data

self.lr_variables = list(self.lr_data.data_vars)

self.hr_variables = list(self.hr_data.data_vars)

  

def __len__(self) -> int:

"""

Get the total number of time steps in the dataset.

  

Returns:

int: The number of time steps.

"""

return self.hr_data.dims["time"]

  

def __getitem__(self, idx: int) -> tuple:

"""

Retrieve a single sample of low-resolution and high-resolution data.

  

Args:

idx (int): Index of the sample to return.

  

Returns:

tuple: A tuple containing:

- lr_norm (torch.Tensor): Normalized low-resolution tensor (C, H, W).

- hr_norm (torch.Tensor): Normalized high-resolution tensor (C, H, W).

"""

# Extract HR and LR data at the given time index

hr_sample = self.hr_data.isel(time=idx).to_array().values # (C, H, W)

lr_sample = self.lr_data.isel(time=idx).to_array().values # (C, H, W)

  

# Convert to torch tensors

hr_sample = torch.tensor(hr_sample, dtype=torch.float32)

lr_sample = torch.tensor(lr_sample, dtype=torch.float32)

  

# Apply Z-score normalization

hr_norm = z_score_normalization(hr_sample)

lr_norm = z_score_normalization(lr_sample)

  

return lr_norm, hr_norm

```