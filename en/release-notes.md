## Compute > Cloud Functions > Release Notes

### July 14, 2026

#### Added Features
- Added environment variable feature
  - Allows separation of code and configuration by setting environment variables for functions.
  - Enables management of sensitive values such as credentials without hardcoding them in the code.
- Added runtime EOL (end of life) indicator feature
  - Displays the deprecated/discontinued status of runtimes in the function list and details.
  - Provides guidance on deprecated runtimes when creating or modifying functions, and restricts the selection and modification of discontinued runtimes.

### April 14, 2026

#### Added Features
- Added Public API v1.0
  - Added support for using Cloud Functions via API.

### January 27, 2026

#### Added Features
- Added function versioning
  - Manage build packages by version and rollback to previous versions.

### November 25, 2025

#### Added Features
- Added API Gateway trigger
  - Added the feature to create API Gateway triggers by utilizing the API Gateway service in the same project.

#### Feature Updates
- Added the latest runtime environment and deprecated some versions
  - Added Go 1.24, 1.25
    - Go 1.22, 1.23 scheduled for deprecation
  - Added Python 3.12, 3.13
  - Deprecated Ruby 2.6.1, added Ruby 3.4.5
  - Deprecated .NET 7, added .NET 8

### July 29. 2025

#### Launch Cloud Functions Service
- Users can write codes by function unit. Functions defined on a specific event are automatically executed, processing the required tasks. Without server management or infrastructure configuration, you can focus only on application logic.