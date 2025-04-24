
```python
```python
# Structure code into functions/classes
def setup_environment(config):
    """Set up logging, seed, etc."""
    # ...

def prepare_data(config):
    """Load and prepare datasets and dataloaders."""
    # ...

def build_model(config):
    """Create and configure model."""
    # ...

def train_epoch(model, train_loader, optimizer, criterion, device, metrics_tracker):
    """Run one epoch of training."""
    # ...

def validate(model, val_loader, criterion, device, metrics_tracker):
    """Run validation."""
    # ...

def test_model(model, test_loader, criterion, device, metrics_tracker):
    """Test the model."""
    # ...

def main():
    """Main training workflow."""
    config = load_config(config_path="cfg/config.yaml")
    setup_environment(config)
    train_loader, val_loader, test_loader = prepare_data(config)
    model, optimizer, criterion = build_model(config)
    
    # Training loop
    for epoch in range(NUM_EPOCHS):
        train_metrics = train_epoch(model, train_loader, optimizer, criterion, device, train_metrics_tracker)
        val_metrics = validate(model, val_loader, criterion, device, val_metrics_tracker)
        # Save metrics, checkpoints, etc.
    
    # Testing
    test_metrics = test_model(model, test_loader, criterion, device, test_metrics_tracker)
    
    # Visualization and saving
    visualize_results(train_metrics, val_metrics, test_metrics, config)

if __name__ == "__main__":
    main()

```
```