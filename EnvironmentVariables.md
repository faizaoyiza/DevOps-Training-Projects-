# Mini Project: Understanding Infrastructure Environments and Environment Variables

## Introduction

In this mini project, I explored how infrastructure environments and environment variables are used to create reusable shell scripts for cloud infrastructure automation. I also implemented command-line arguments, input validation, and AWS CLI commands to dynamically provision cloud resources depending on the selected environment.

---

# Understanding Infrastructure Environments

Infrastructure environments represent the different stages where an application is developed, tested, and deployed.

For this project, three environments were considered:

- **Development Environment** – Local Ubuntu machine used for development.
- **Testing Environment** – EC2 instance hosted in the AWS Testing account.
- **Production Environment** – EC2 instance hosted in the AWS Production account.

Each environment has its own resources and configuration while using the same shell script.

---

# Understanding Environment Variables

Environment variables are key-value pairs that allow scripts to behave differently depending on the environment in which they are executed.

Example configuration:

## Development

```bash
DB_URL=localhost
DB_USER=dev_user
DB_PASS=dev_password
```

## Testing

```bash
DB_URL=testing-db.example.com
DB_USER=test_user
DB_PASS=test_password
```

## Production

```bash
DB_URL=production-db.example.com
DB_USER=prod_user
DB_PASS=prod_password
```

Instead of modifying the script whenever the environment changes, these values are loaded dynamically.

---

# Creating the Shell Script

The project began by creating a shell script named:

```bash
aws_cloud_manager.sh
```

The script was made executable using:

```bash
chmod +x aws_cloud_manager.sh
```

---

# Accepting Command-Line Arguments

Rather than hard-coding values, the script accepts the target environment as a command-line argument.

Example:

```bash
./aws_cloud_manager.sh development
```

or

```bash
./aws_cloud_manager.sh testing
```

or

```bash
./aws_cloud_manager.sh production
```

Inside the script:

```bash
ENVIRONMENT=$1
```

This makes the script reusable for multiple environments.

---

# Validating User Input

The script first checks that exactly one argument has been supplied.

```bash
if [ "$#" -ne 1 ]; then
    echo "Usage: $0 {development|testing|production}"
    exit 1
fi
```

If no environment or multiple environments are supplied, the script exits with an informative error message.

---

# Dynamic Environment Configuration

The script uses conditional statements to load different configuration values depending on the selected environment.

```bash
if [ "$ENVIRONMENT" = "development" ]; then

    INSTANCE_NAME="Dev-Server"
    INSTANCE_TYPE="t2.micro"
    DB_URL="localhost"

elif [ "$ENVIRONMENT" = "testing" ]; then

    INSTANCE_NAME="Test-Server"
    INSTANCE_TYPE="t2.small"
    DB_URL="testing-db.example.com"

elif [ "$ENVIRONMENT" = "production" ]; then

    INSTANCE_NAME="Production-Server"
    INSTANCE_TYPE="t2.medium"
    DB_URL="production-db.example.com"

else

    echo "Invalid environment."

    echo "Choose development, testing or production."

    exit 1

fi
```

This demonstrates that the script behaves differently depending on the environment selected by the user.

---

# AWS CLI Integration

One of the primary objectives of this project was to integrate AWS CLI into the shell script.

After determining the environment, the script provisions an EC2 instance dynamically using AWS CLI.

Example:

```bash
aws ec2 run-instances \
--image-id ami-0abcdef1234567890 \
--count 1 \
--instance-type $INSTANCE_TYPE \
--key-name my-keypair \
--security-groups default \
--tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=$INSTANCE_NAME}]"
```

The values passed to the AWS CLI command depend on the environment selected.

For example:

### Development

- Instance Name: Dev-Server
- Instance Type: t2.micro

### Testing

- Instance Name: Test-Server
- Instance Type: t2.small

### Production

- Instance Name: Production-Server
- Instance Type: t2.medium

This allows one script to provision different infrastructure without changing the code.

---

# Improved Error Handling

Additional validation was added to improve the user experience.

If an invalid environment is entered:

```bash
./aws_cloud_manager.sh demo
```

The script displays:

```text
Invalid environment.

Please choose one of the following:

development
testing
production
```

If AWS CLI is not installed, the script also checks for it before execution.

```bash
if ! command -v aws &> /dev/null
then
    echo "AWS CLI is not installed."

    exit 1
fi
```

Similarly, the script verifies that AWS credentials have been configured.

```bash
aws sts get-caller-identity
```

If authentication fails, the script exits and instructs the user to configure AWS credentials.

---

# Key Concepts Learned

Through this project, I learned how to:

- Differentiate between infrastructure environments and environment variables.
- Use environment variables to make scripts reusable.
- Accept user input using positional parameters.
- Validate command-line arguments.
- Implement conditional logic based on environment selection.
- Improve user feedback through meaningful error messages.
- Integrate AWS CLI commands into shell scripts.
- Dynamically provision EC2 instances using different configurations.
- Build flexible automation scripts suitable for DevOps workflows.

---

# Conclusion

This mini project strengthened my understanding of shell scripting, environment variables, and infrastructure automation using AWS CLI. By combining command-line arguments, conditional logic, environment-specific configurations, and AWS CLI commands, I developed a reusable automation script capable of provisioning different EC2 instances for development, testing, and production environments.

Overall, the project demonstrated how DevOps engineers use a single script to automate cloud infrastructure deployment while adapting dynamically to different environments, reducing manual configuration and improving consistency.
