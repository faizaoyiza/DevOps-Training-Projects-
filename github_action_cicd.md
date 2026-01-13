# Module 3: Implementing Continuous Integration (CI)

This module covers the fundamentals of Continuous Integration using **GitHub Actions**, focusing on building, testing, and managing workflows efficiently.

---

## Lesson 1: Building and Testing Code

### Objectives
- Set up build steps in GitHub Actions
- Run automated tests as part of the CI process

---

## Setting Up Build Steps

GitHub Actions workflows are defined using YAML files stored in:

```
.github/workflows/main.yml
```

### Defining the Build Job

A **job** is a collection of steps executed on the same runner.

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
```

### Adding Build Steps

Each step performs a specific task such as checking out code, installing dependencies, or building the application.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Build application
        run: npm run build
```

### Running Tests

Tests ensure that the code works correctly before deployment.

```yaml
      - name: Run tests
        run: npm test
```

---

### Learner Notes
- The build job typically includes code checkout, dependency installation, build, and test steps.
- `runs-on: ubuntu-latest` specifies the operating system for the runner.
- GitHub-maintained actions simplify common CI tasks.
- Standard Node.js commands include `npm install`, `npm run build`, and `npm test`.

---

## Additional YAML Concepts in GitHub Actions

### Objectives
- Understand advanced YAML features
- Use environment variables and secrets
- Apply conditional execution
- Share data between workflow steps

---

## Environment Variables

Environment variables can be defined at the workflow, job, or step level.

### Workflow-Level Variable

```yaml
env:
  CUSTOM_VAR: my_value
```

### Using the Variable

```yaml
jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - name: Use environment variable
        run: echo $CUSTOM_VAR
```

---

## Working with Secrets

Secrets store sensitive information securely.

### Using a Secret

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Use secret
        run: echo "Access Token: ${{ secrets.ACCESS_TOKEN }}"
```

> Secrets are configured in **Repository Settings → Secrets and Variables → Actions**

---

## Conditional Execution

Conditions control when a job or step runs.

```yaml
jobs:
  conditional-job:
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v4
```

---

## Sharing Outputs Between Steps

Outputs allow data to be passed between steps in the same job.

### Setting an Output

```yaml
- id: step-one
  run: echo "value=hello" >> $GITHUB_OUTPUT
```

### Using the Output

```yaml
- id: step-two
  run: echo "Received value: ${{ steps.step-one.outputs.value }}"
```

---

### Learner Notes
- Environment variables help manage configuration dynamically.
- Secrets protect sensitive credentials.
- Conditional execution improves workflow efficiency.
- Outputs enable more advanced and flexible CI pipelines.

---

## Lesson 2: Configuring Build Matrices

### Objectives
- Implement parallel builds
- Test across multiple environments
- Manage dependency compatibility

---

## Build Matrix Example

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16, 18, 20]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}

      - run: npm install
      - run: npm test
```
<img width="947" height="470" alt="Image" src="https://github.com/user-attachments/assets/ad505090-a847-4cfb-89ed-f7dcaafab410" />


<img width="950" height="463" alt="Image" src="https://github.com/user-attachments/assets/ed87a24d-09a3-4604-96ff-a8909d8d8a15" />
### Explanation
- The job runs in parallel for each Node.js version.
- Helps identify version-specific issues early.
- Improves reliability across environments.

---

## Conclusion

This module demonstrates how Continuous Integration with GitHub Actions automates building, testing, and validating code changes. Using advanced YAML features such as matrices, secrets, and conditions improves scalability, security, and efficiency in CI/CD pipelines.

