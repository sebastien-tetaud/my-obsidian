

# How Uber Serves Over 40 Million Reads Per Second from Online Storage Using an Integrated Cache

https://www.uber.com/en-IT/blog/how-uber-serves-over-40-million-reads-per-second-using-an-integrated-cache/


# How Uber Migrated Financial Data from DynamoDB to Docstore

https://www.uber.com/en-IT/blog/dynamodb-to-docstore-migration/


## EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks

https://arxiv.org/pdf/1905.11946


## Vision Transformers

https://arxiv.org/pdf/2010.11929v2

## Exploring Explainability for Vision Transformers

https://github.com/jacobgil/vit-explain/tree/main?tab=readme-ov-file
https://jacobgil.github.io/deeplearning/vision-transformer-explainability



```python

def normalize(data_array):

"""

Normalize the data array by dividing by 1000.

Values > 1 are clipped to 1.

Returns both the normalized data and a valid mask per band.

"""

normalized_data = []

valid_masks = []

  

for i in range(data_array.shape[2]):

band_data = data_array[:, :, i].astype(np.float32)

  

# Valid mask: original (before division)

valid_mask = band_data > 0

  

# Normalize

band_data = band_data / 1000.0

band_data = np.clip(band_data, 0.0, 1.0)

  

# Set invalid pixels to 0

band_data[~valid_mask] = 0.0

  

normalized_data.append(band_data)

valid_masks.append(valid_mask)

  

return np.dstack(normalized_data), np.dstack(valid_masks)
```