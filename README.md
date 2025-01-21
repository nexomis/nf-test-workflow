# Nextflow Pipeline Test Workflow

A reusable GitHub Actions workflow for testing Nextflow pipelines.

## Features

- Automatically discovers and runs all test*.yml files in data/test directory
- Sets up Java, Nextflow, and Apptainer environments
- Configurable Nextflow and Java versions
- Handles submodules recursively
- Reports test failures

## Usage

1. Create a `.github/workflows` directory in your pipeline repository if it doesn't exist:
```bash
mkdir -p .github/workflows
```

2. Create a workflow file (e.g., `.github/workflows/test.yml`) with the following content:
```yaml
name: Test Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    uses: nexomis/nf-test-workflow/.github/workflows/nf-test.yml@main
    with:
      nextflow_version: '24.10.3'  # optional, defaults to 24.10.3
      java_version: '17'           # optional, defaults to 17
```

## Test File Structure

Place your test files in the `data/test` directory with names matching `test*.yml`. For example:

```
data/
└── test/
    ├── test.yml
    ├── test_case1.yml
    └── test_case2.yml
```

Each test file should be a valid YAML file containing the parameters needed to run your pipeline. Example:

```yaml
# data/test/test.yml
in_dir: "data/test/input_dir"
out_dir: "data/test/results"
```

## Requirements

- Your pipeline must:
  - Have a `data/test` directory containing test*.yml files
  - Use Nextflow's params-file feature for parameter input
  - Have proper error handling that exits with non-zero status on failure
  - Include all necessary submodules if using git submodules

## Environment

The workflow sets up:
- Ubuntu latest
- Java (configurable version, default: 17)
- Nextflow (configurable version, default: 24.10.3)
- Apptainer 1.3.6

## Error Handling

The workflow will:
1. Fail if any test file execution returns a non-zero exit code
2. Report which test file failed
3. Stop execution after the first failure

## Example Pipeline Integration

Here's a complete example of how to integrate this workflow into your pipeline:

1. Create the workflow file:

```yaml
# .github/workflows/test.yml
name: Test Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    uses: nexomis/nf-test-workflow/.github/workflows/nf-test.yml@main
```

2. Create a test file:

```yaml
# data/test/test.yml
in_dir: "data/test/input_dir"
out_dir: "data/test/results"
```

3. Ensure your pipeline handles the test parameters correctly:

```nextflow
// main.nf
params.in_dir = null
params.out_dir = null

// Validate parameters
if (!params.in_dir) {
    error "Input directory not specified"
}
if (!params.out_dir) {
    error "Output directory not specified"
}
```

The workflow will automatically run all test files and report any failures.
