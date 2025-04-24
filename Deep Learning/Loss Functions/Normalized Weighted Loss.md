# Weighted Loss for Images with Varying Valid Pixel Percentages: A Summary

## Problem Statement
When training models on datasets where images have varying numbers of valid pixels (due to masks, missing data, etc.), standard loss functions face challenges:
- Images with few valid pixels contribute less to the overall loss
- Models may ignore or underperform on samples with limited valid regions
- Training becomes biased toward samples with more valid pixels

## Proposed Solution: Valid Pixel Weighted Loss

### Key Components:
1. **Valid Pixel Detection**: Create binary masks identifying valid pixels (where ground truth ≥ 0)
2. **Per-Sample Weighting**: Weight each sample based on its valid pixel ratio
   - Calculate ratio: r = valid_pixels / total_pixels
   - Apply square root transformation: weight = √r 
   - This balances between equal sample weighting and equal pixel weighting
3. **Loss Computation**: Calculate loss only on valid pixels for each sample
4. **Weighted Aggregation**: Sum the weighted sample losses
5. **Normalization**: Divide by the total weights to get a properly scaled average

### Algorithm Implementation
```python
def valid_pixel_weighted_loss(y_pred, y_true, base_loss_fn):
    batch_size = y_pred.shape[0]
    total_weighted_loss = 0.0
    total_weight = 0.0
    
    for i in range(batch_size):
        # Get masks for valid pixels (where ground truth >= 0)
        valid_mask = (y_true[i] >= 0)
        valid_pixel_count = valid_mask.sum()
        total_pixel_count = valid_mask.size
        
        # Skip samples with no valid pixels
        if valid_pixel_count == 0:
            continue
            
        # Calculate weight using square root of valid pixel ratio
        valid_ratio = valid_pixel_count / total_pixel_count
        weight = math.sqrt(valid_ratio)
        
        # Compute loss only on valid pixels
        sample_loss = base_loss_fn(
            y_pred[i][valid_mask], 
            y_true[i][valid_mask]
        )
        
        # Add weighted loss to total
        total_weighted_loss += sample_loss * weight
        total_weight += weight
    
    # Return normalized weighted average
    if total_weight > 0:
        return total_weighted_loss / total_weight
    return 0.0  # Handle edge case with no valid pixels
```

## Importance of using normalization:

1. **Scale Invariance**: Without normalization, the total loss value would depend on the number of samples and their weights. This means the absolute value of the loss would vary significantly between batches with different numbers of valid samples, making it difficult to:
    
    - Compare loss values across training runs
    - Set consistent learning rates
    - Determine convergence
2. **Gradient Stability**: Normalization keeps gradients at a consistent scale regardless of how many valid samples are in a batch, preventing:
    
    - Gradient explosion when many samples contribute
    - Excessively small gradients when few samples contribute
3. **Balanced Learning**: Without normalization, the model would effectively give more importance to batches with more valid pixels, potentially biasing learning toward certain data distributions.
    
4. **Statistical Interpretation**: The normalized result represents a proper weighted average, making it a statistically meaningful quantity (expected loss per valid sample).

## Benefits of This Approach
- **Fairness**: Even samples with few valid pixels receive appropriate attention
- **Stability**: Training progresses consistently regardless of valid pixel distribution
- **Flexibility**: Works with any base loss function (MSE, cross-entropy, etc.)
- **Balance**: Square root weighting prevents both extremes - treating all samples equally or all pixels equally

This weighted loss approach effectively handles the complexities of training on datasets with variable valid pixel counts while maintaining training stability.


## Combine Loss


# For MSE
weighted_mse = valid_pixel_weighted_loss(y_pred, y_true, mse_loss_fn)

# For SAM
weighted_sam = valid_pixel_weighted_loss(y_pred, y_true, sam_loss_fn)

# Combine weighted losses
combined_loss = self.alpha * weighted_sam + self.beta * weighted_mse
