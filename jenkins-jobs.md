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
