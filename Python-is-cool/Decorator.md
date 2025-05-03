Create a benchmark function to measure a processing time.

```python
def benchmark(func):
    def wrapper(*args, **kwargs):
        t1 = time.time()
        result = func(*args, **kwargs)
        t2 = time.time() - t1
        wrapper.execution_time = t2  # Store execution time as an attribute
        print(f"{func.__name__} ran in {t2} seconds")
        return result
    return wrapper
```

add the Decorator the the load_config function.

```python
@benchmark
def load_config(file_path: str) -> dict:
    """
    Load YAML file.

    Args:
        file_path (str): Path to the YAML file.

    Returns:
        dict: Dictionary containing configuration information.
    """
    with open(file_path, 'r') as file:
        config = yaml.safe_load(file)
    return config
```

```python
config = load_config(file_path="config.yaml")
query = config["cds_request"]
print(load_config.execution_time)
load_config ran in 0.00769495964050293 seconds
```