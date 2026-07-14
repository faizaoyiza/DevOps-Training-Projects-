# ⚙️ Jenkins Jobs: Freestyle & Pipeline Projects

This guide explains how to create **Freestyle Jobs** and **Pipeline Jobs** in Jenkins, including integration with GitHub and automation using webhooks.

--- 

## 🔹 What is a Jenkins Job?
A **Jenkins job** is a unit of work executed by the Jenkins automation server. Jobs automate steps such as:
- Compiling code  
- Running tests  
- Packaging applications  
- Deploying to servers  

Jobs can be configured with:
- **Build steps**  
- **Post-build actions**  
- **Source Code Management (SCM)** integration  
- **Triggers** (e.g., GitHub webhooks)  

---

## 🔹 Creating a Freestyle Project

1. From the **Dashboard menu**, click **New Item**.  
2. Enter a name (e.g., `my-first-job`).  
3. Select **Freestyle Project** → Click **OK**.  
4. Add a simple build step:
   ```bash
   echo "Hello Jenkins! This is my first job."
   ```
5. Save and click **Build Now**.  

✅ A successful job execution will appear in the **Build History**.

<img width="943" height="469" alt="Image" src="https://github.com/user-attachments/assets/6051e354-cada-4d5a-9fd4-1a97146d5970" />

---
<img width="946" height="476" alt="Image" src="https://github.com/user-attachments/assets/8c4232da-4513-49fe-8856-53df2dbb6f74" />
## 🔹 Connecting Jenkins to GitHub (SCM Integration)

1. Create a new GitHub repository called `jenkins-scm` (with a `README.md`).  
2. In your Jenkins job configuration:  
   - Under **Source Code Management (SCM)**, select **Git**.  
   - Paste your repository URL.  
   - Ensure branch is set to `main`.  
3. Save and click **Build Now**.  

✅ Jenkins will now pull code from your GitHub repo into the job.

---
<img width="935" height="477" alt="Image" src="https://github.com/user-attachments/assets/3be0d388-6dac-4cc4-a88f-3866384c2332" />
<img width="932" height="485" alt="Image" src="https://github.com/user-attachments/assets/f8a6e503-3871-4648-941e-0237b71074b0" />
## 🔹 Configuring Build Triggers

We can automate builds so they run on every code change:  

1. Go to **Configure Job** → **Build Triggers**.  
2. Enable **GitHub hook trigger for GITScm polling**.  
3. In your GitHub repo, create a **Webhook**:  
   - Payload URL → `http://<JENKINS-IP>:8080/github-webhook/`  
   - Content type → `application/json`  

4. Save webhook settings.  

✅ Whenever you push changes to GitHub, Jenkins will automatically run a new build.

---
<img width="927" height="472" alt="Image" src="https://github.com/user-attachments/assets/26684210-c538-477a-ba30-b400ba4ec963" />
## 🔹 Creating a Pipeline Job

Unlike Freestyle Jobs, Pipelines are defined as **code** using a `Jenkinsfile`.  

### Example Jenkinsfile
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to server...'
            }
        }
    }
}
```

### Steps:
1. In Jenkins Dashboard → **New Item** → Select **Pipeline**.  
2. Name it (e.g., `my-first-pipeline`).  
3. Under **Pipeline Definition**, point to your GitHub repo containing the `Jenkinsfile`.  
4. Save and click **Build Now**.  

✅ Jenkins will execute the stages defined in your pipeline.

---

## ✅ Summary
- **Freestyle Jobs** = simple, GUI-based configuration for tasks.  
- **Pipeline Jobs** = CI/CD as code with Jenkinsfile.  
- **GitHub integration + webhooks** = fully automated builds on commits.  

With both approaches, Jenkins enables automation of builds, tests, and deployments in DevOps workflows.
# Mini Project: Configuring Jenkins Build Triggers and Creating a CI/CD Pipeline

## Introduction

In this mini project, I configured a Jenkins build trigger using a GitHub webhook and created a Jenkins Pipeline to automate the Continuous Integration and Continuous Delivery (CI/CD) process. The pipeline connects to a GitHub repository, builds a Docker image, and deploys the application inside a Docker container automatically whenever changes are pushed to the repository.

---

# Configuring the Jenkins Build Trigger

The first task was to configure Jenkins so that every push to the GitHub repository automatically triggers a new build.

### Steps Performed

1. Opened the Jenkins dashboard.
2. Selected the existing pipeline job.
3. Clicked **Configure**.
4. Navigated to the **Build Triggers** section.
5. Enabled:

```text
GitHub hook trigger for GITScm polling
```

This allows Jenkins to listen for events sent from GitHub through a webhook.

Since the GitHub webhook had already been configured in an earlier project, no additional webhook setup was required. Once connected, every push to the repository automatically triggered a new Jenkins build.

---

# Writing the Jenkins Pipeline

A Jenkins Pipeline defines the sequence of stages required to build, test, and deploy an application automatically.

Jenkins supports two pipeline syntaxes:

- **Declarative Pipeline** – Structured, easier to read, and recommended for most projects.
- **Scripted Pipeline** – More flexible and suitable for advanced scripting.

For this project, I used a **Declarative Pipeline**.

---

# Pipeline Stages

The pipeline consisted of three major stages.

## Stage 1 – Connect to GitHub

This stage checks out the latest source code from the GitHub repository.

```groovy
checkout scmGit(
    branches: [[name: '*/main']],
    extensions: [],
    userRemoteConfigs: [[url: 'https://github.com/your-username/your-repository.git']]
)
```

This ensures Jenkins always builds the latest version of the project from the **main** branch.

---

## Stage 2 – Build Docker Image

After downloading the source code, Jenkins builds a Docker image.

```bash
docker build -t dockerfile .
```

This command creates a Docker image named **dockerfile** using the Dockerfile located in the project directory.

---

## Stage 3 – Run Docker Container

Once the image is successfully built, Jenkins starts a Docker container.

```bash
docker run -itd --name nginx -p 8081:80 dockerfile
```

This command:

- Creates a container named **nginx**
- Runs it in detached mode
- Maps container port **80** to host port **8081**

After deployment, the application becomes accessible through port **8081**.

---

# Generating the Git Checkout Script

Instead of manually writing the Git checkout syntax, Jenkins provides a Pipeline Syntax Generator.

The steps performed were:

1. Open the Pipeline job.
2. Click **Pipeline Syntax**.
3. Select:

```
checkout: Check out from version control
```

4. Enter the GitHub repository URL.
5. Select the **main** branch.
6. Click **Generate Pipeline Script**.
7. Copy the generated script into the Jenkinsfile.

This reduces errors and ensures the correct syntax is used.

---

# Installing Docker on the Jenkins Server

Before Jenkins could execute Docker commands, Docker needed to be installed on the same server hosting Jenkins.

I created a shell script named:

```bash
docker.sh
```

The script was made executable using:

```bash
chmod +x docker.sh
```

Then executed with:

```bash
./docker.sh
```

The installation completed successfully, allowing Jenkins to interact with Docker during pipeline execution.

---

# Creating the Dockerfile

Since Docker cannot build an image without a Dockerfile, I created a file named:

```text
Dockerfile
```

The Dockerfile contained instructions for creating a simple Nginx web server.

Example:

```dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

This image copies the custom webpage into the default Nginx directory.

---

# Creating the Application

Next, I created an `index.html` file containing a simple webpage.

Example:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Jenkins Pipeline</title>
</head>
<body>
    <h1>Congratulations!</h1>
    <p>You have successfully run your first Jenkins Pipeline.</p>
</body>
</html>
```

Both the **Dockerfile** and **index.html** were committed and pushed to the GitHub repository.

---

# Automatic Pipeline Execution

Once the changes were pushed to GitHub, the configured webhook immediately notified Jenkins.

Jenkins automatically:

1. Pulled the latest source code.
2. Built the Docker image.
3. Started the Docker container.

This demonstrated a fully automated CI/CD workflow.

---

# Resolving Docker Permission Issues

During the first build, Jenkins was unable to execute Docker commands because the Jenkins user did not have permission to access Docker.

The issue was resolved by adding the Jenkins user to the Docker group:

```bash
sudo usermod -aG docker jenkins
```

After updating the permissions, I restarted both Docker and Jenkins.

```bash
sudo systemctl restart docker
sudo systemctl restart jenkins
```

The pipeline then executed successfully without any permission errors.

---

# Accessing the Application

Since the Docker container exposed port **8081**, I updated the AWS EC2 Security Group by adding an inbound rule to allow traffic on port **8081**.

After opening the port, I accessed the deployed application using:

```text
http://<EC2-Public-IP>:8081
```

Example:

```text
http://54.89.53.188:8081
```

The browser displayed the custom `index.html` page, confirming that the Jenkins pipeline had successfully built and deployed the application.

---

# Key Concepts Learned

Through this mini project, I learned how to:

- Configure Jenkins build triggers using GitHub webhooks.
- Create Declarative Jenkins Pipelines.
- Generate Git checkout syntax using the Jenkins Pipeline Syntax Generator.
- Install Docker on a Jenkins server using a shell script.
- Build Docker images automatically from Jenkins.
- Deploy Docker containers within a Jenkins Pipeline.
- Resolve Docker permission issues for the Jenkins user.
- Configure AWS Security Groups to expose container ports.
- Automate the entire build and deployment process using CI/CD.

---

# Conclusion

This project strengthened my understanding of Jenkins Pipelines and Continuous Integration/Continuous Delivery (CI/CD). I successfully configured Jenkins to automatically trigger builds whenever changes were pushed to GitHub, eliminating the need for manual deployments.

I also learned how Docker integrates with Jenkins to build and deploy applications automatically. By combining GitHub webhooks, Jenkins Pipelines, Docker, and AWS, I created a simple but effective automated deployment workflow. This project provided valuable hands-on experience with DevOps automation and laid the foundation for building more advanced CI/CD pipelines in future projects.
