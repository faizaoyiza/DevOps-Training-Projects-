# Mini Project: Understanding Infrastructure Environments and Environment Variables

## Introduction

In this mini project, I explored two fundamental concepts in software development and cloud computing: **Infrastructure Environments** and **Environment Variables**. Although both concepts contain the word *environment*, they serve different purposes. Understanding the difference between them is essential for developing flexible, reusable, and maintainable automation scripts.

This project also introduced the basics of shell scripting by creating an `aws_cloud_manager.sh` script that uses environment variables, command-line arguments, and input validation to determine which infrastructure environment the script should target.

---

# Understanding Infrastructure Environments

Infrastructure environments are the different stages through which an application progresses during its lifecycle. Each environment serves a specific purpose and ensures that software is developed, tested, and deployed safely.

For this project, I considered the example of a FinTech application with three separate environments:

- **Development Environment:** A local Ubuntu machine running on VirtualBox where developers build and test the application.
- **Testing Environment:** An EC2 instance hosted in the first AWS account where the application undergoes further testing.
- **Production Environment:** An EC2 instance hosted in a second AWS account where the live application is deployed for customers.

Each environment has its own infrastructure and configuration, allowing development, testing, and production activities to remain isolated from one another.

---

# Understanding Environment Variables

Environment variables are **key-value pairs** that store configuration settings used by scripts and applications. Rather than hard-coding configuration values, environment variables allow applications to adjust automatically depending on the environment in which they are running.

For example, a FinTech application that connects to a database requires different connection details for each environment.

## Development Environment

```bash
DB_URL=localhost
DB_USER=test_user
DB_PASS=test_pass
```

These variables point to a local database where developers can safely test new features.

## Testing Environment

```bash
DB_URL=testing-db.example.com
DB_USER=testing_user
DB_PASS=testing_pass
```

These variables connect the application to a dedicated testing database that closely resembles the production environment.

## Production Environment

```bash
DB_URL=production-db.example.com
DB_USER=prod_user
DB_PASS=prod_pass
```

These values connect the application to the live production database used by customers.

Using environment variables allows the same application code to be deployed across multiple environments without modifying the source code.

---

# Creating the Shell Script

To begin applying these concepts, I created a shell script named:

```bash
aws_cloud_manager.sh
```

After creating the file, I made it executable using:

```bash
sudo chmod +x aws_cloud_manager.sh
```

The first version of the script checked whether an environment variable named `ENVIRONMENT` had been set.

If no environment variable existed, the script automatically executed the `else` block because there was no environment information available.

I then exported an environment variable from the terminal:

```bash
export ENVIRONMENT=production
```

Running the script after exporting this variable caused it to recognize that it was operating in the **production** environment.

This demonstrates how environment variables allow a script to change its behavior dynamically without modifying the script itself.

---

# Hard-Coding vs Dynamic Configuration

I also explored the difference between hard-coded values and dynamic configuration.

For example:

```bash
ENVIRONMENT=testing
```

Placing this line inside the script forces it to always run as though it is in the testing environment.

Although this works, it is not considered a good practice because changing environments would require editing the script every time it is executed.

A better solution is to allow users to specify the environment when running the script.

---

# Using Positional Parameters

Shell scripting provides **positional parameters**, which allow scripts to accept values at runtime.

Inside the script:

```bash
ENVIRONMENT=$1
```

The variable `$1` represents the first argument supplied when executing the script.

Example:

```bash
./aws_cloud_manager.sh testing
```

In this case:

- `$1` = `testing`
- `ENVIRONMENT` = `testing`

The script can also accept multiple arguments.

Example:

```bash
./aws_cloud_manager.sh testing 5
```

Inside the script:

```bash
ENVIRONMENT=$1
NUMBER_OF_INSTANCES=$2
```

Where:

- `$1` stores the selected environment.
- `$2` stores the number of EC2 instances to provision.

This makes the script more flexible and reusable across different environments.

---

# Validating User Input

To make the script more reliable, I implemented argument validation.

The script checks whether exactly **one argument** has been supplied before continuing execution.

The validation uses:

- `$#` – Returns the number of arguments passed to the script.
- `-ne` – Means "not equal."
- `$0` – Represents the script's filename.

If the required argument is missing or too many arguments are provided, the script displays a helpful usage message and exits with a status code of **1**.

This validation helps prevent execution errors and provides users with clear guidance on how to run the script correctly.

---

# Key Concepts Learned

Through this mini project, I learned:

- The difference between infrastructure environments and environment variables.
- How development, testing, and production environments are separated in real-world deployments.
- How environment variables make scripts dynamic by storing configuration values outside the code.
- How to use the `export` command to define environment variables.
- How positional parameters (`$1`, `$2`) allow scripts to accept user input during execution.
- How to validate command-line arguments using `$#`, `-ne`, and exit codes.
- Why avoiding hard-coded values makes scripts more reusable and maintainable.

---

# Conclusion

This mini project provided a practical introduction to infrastructure environments, environment variables, and shell scripting.

I learned that infrastructure environments represent the different stages of application deployment, while environment variables provide a flexible way to configure applications without modifying the code. I also gained hands-on experience using the `export` command, positional parameters, and argument validation to build more dynamic and reliable shell scripts.

Overall, this project established a strong foundation for writing reusable automation scripts that can efficiently manage cloud infrastructure across multiple environments, an essential skill in DevOps engineering.
