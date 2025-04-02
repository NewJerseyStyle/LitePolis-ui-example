# LitePolis UI Template

This repository serves as a template for creating UI modules for LitePolis. It provides a basic structure and example code to guide you through the process of building user interfaces.

> :warning: Keep the prefix "litepolis-ui-" and "litepolis_ui_" in the name of package and directories to ensure the LitePolis package manager will be able to recognize it during deployment.

## Getting Started

* **`setup.py`**: This file contains metadata about your package. **Crucially**, you need to change the `name` field to your package's unique name. Also, update the `version`, `description`, `author`, and `url` fields as needed.

* **`litepolis_ui_template/core.py`**: This file contains the core logic for your UI module. It defines how static files are served and includes the `DEFAULT_CONFIG` dictionary. The `DEFAULT_CONFIG` dictionary provides default configuration settings that will be registered with LitePolis.  **Important:**  This file sets up the serving of your UI's static files.  You can also include UI specific configuration in `DEFAULT_CONFIG`.

* **`tests/test_core.py`**: This file contains tests for your module. Update the tests to reflect your UI module's functionality. Thorough testing is essential for ensuring the correctness of your module. Ensure your tests correctly set up the FastAPI test application to serve your static files and utilize `DEFAULT_CONFIG` as needed.


## About `DEFAULT_CONFIG`

This dictionary, defined in `core.py`, holds default configuration values for your UI module. These values will be registered with the LitePolis configuration system when the module is deployed. If modified configurations are provided during deployment, they will override these defaults. Settings can be fetched within your code (or other services) using the `get_config(<package-name>, <configuration-key>)` function provided by LitePolis infrastructure, which will return the currently active value.  For UI modules, this can be used to configure aspects of the UI, such as theming or default settings.

## Recommended Pattern for Accessing Configuration

To ensure automated tests (Pytest) do not rely on live configuration sources, use the following pattern to fetch configuration values. This pattern checks for environment variables set by Pytest (`PYTEST_CURRENT_TEST` or `PYTEST_VERSION`) to determine the execution context.

```python
import os
# Assuming get_config is available for fetching live config
# from litepolis import get_config

# Define default values suitable for testing environment
DEFAULT_CONFIG = {
    "database_url": "sqlite:///./test_default.db",
    "some_api_key": "test_key_123"
    # Add other necessary default config values here
}

# Configuration Fetching Logic
db_url = None
some_key = None

# Check if running under Pytest
if ("PYTEST_CURRENT_TEST" not in os.environ and
    "PYTEST_VERSION" not in os.environ):
    # NOT running under Pytest: Fetch from live source
    print("Fetching configuration from live source...") # Optional debug msg
    # Replace with actual service name and key
    # db_url = get_config("your_service_name", "database_url")
    # some_key = get_config("your_service_name", "some_api_key")
    # Example placeholder if get_config isn't immediately available:
    db_url = "live_db_url_placeholder"
    some_key = "live_api_key_placeholder"
else:
    # Running under Pytest: Use default values
    print("Running under Pytest. Using default configuration.") # Optional debug msg
    db_url = DEFAULT_CONFIG["database_url"]
    some_key = DEFAULT_CONFIG["some_api_key"]

# Use the determined config values (db_url, some_key)
print(f"Using Database URL: {db_url}")
print(f"Using API Key: {some_key}")

```
