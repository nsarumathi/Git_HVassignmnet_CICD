# Flask Application CI/CD Implementation
------------------------------------------------------------
This repository demonstrates the implementation of automated CI/CD pipelines for a Python Flask web application using two industry-standard tools: Jenkins and GitHub Actions.

🚀 Overview
-----------------------------------------
The goal of this project is to automate the lifecycle of a Flask application, ensuring that every code change is automatically built, tested, and prepared for deployment.

🛠 Project 1: Jenkins CI/CD Pipeline
-------------------------------------------------------------
**Objective**
Automate the testing and deployment process using a Jenkins Pipeline (Jenkinsfile).

**Setup:**
1.Install Jenkins Server on Cloud Instance(AWS EC2)

a.Launch EC2 Instance:

<img width="1727" height="772" alt="EC1" src="https://github.com/user-attachments/assets/675e8c75-4dde-4676-b8c6-ebcc9d11ba18" />

b.Install & enable Jenkins:

<img width="1597" height="795" alt="EC2_Jenkins" src="https://github.com/user-attachments/assets/181d6694-2d9f-468b-9654-a780076cc9e6" />

c.Jenkins Configuration:

<img width="1487" height="957" alt="Jenkins_Setup" src="https://github.com/user-attachments/assets/d6dcccb9-8521-4a93-88e3-91d8d94005e9" />
<img width="1677" height="907" alt="Jenkins" src="https://github.com/user-attachments/assets/f61166b5-f38e-4c3a-ad7d-b878f82879ae" />

2.Setup Python virtual environment
    sudo apt update
    sudo apt install python3-venv -y
    
3.Create JenkinsFile in Repository 
      pipeline {
      agent any
  
      environment {
          VENV = 'venv'
          EMAIL = 'sarumathinallasivam97@gmail.com'
      }
  
      triggers {
          githubPush()   // Auto trigger on GitHub push
      }
  
      stages {
  
          stage('Checkout') {
              steps {
                 checkout scm
              }
          }
          stage('Build') {
              steps {
                  sh '''
                  python3 -m venv $VENV
                  . $VENV/bin/activate
                  pip install --upgrade pip
                  pip install -r requirements.txt
                  '''
              }
          }
  
          stage('Test') {
              steps {
                  sh '''
                  . $VENV/bin/activate
                  pip install pytest
                  pytest || exit 1
                  '''
              }
          }
  
          stage('Deploy') {
              when {
                  expression { currentBuild.currentResult == 'SUCCESS' }
              }
              steps {
                  sh '''
                  echo "Deploying to staging..."
                  nohup python app.py > app.log 2>&1 &
                  '''
              }
          }
      }
  
      post {
          success {
              echo 'Build SUCCESS'
              mail to: "${EMAIL}",
                   subject: "Jenkins SUCCESS: ${env.JOB_NAME}",
                   body: "Build succeeded! Check Jenkins."
          }
  
          failure {
              echo 'Build FAILED'
              mail to: "${EMAIL}",
                   subject: "Jenkins FAILED: ${env.JOB_NAME}",
                   body: "Build failed! Check logs immediately."
          }
      }
  }
4.Configure Pipeline Job in Jenkin Server

<img width="1676" height="978" alt="PipelineJob" src="https://github.com/user-attachments/assets/34493324-ad61-4287-8092-75b219c20cff" />

Pipeline Script:
-------------------
<img width="1113" height="792" alt="JenkinBuild_sucess" src="https://github.com/user-attachments/assets/dff691bf-0181-46ba-87fa-3806bcdd5cc1" />

5.Configure triggers & Email notifications setup in Jenkin server

Triggers:
------------------------
<img width="923" height="571" alt="GithubPushTriggerSetup" src="https://github.com/user-attachments/assets/b6888595-93c7-46ee-9dcf-2f10f9b7d04f" />

Email Notification :
-------------------------
<img width="1768" height="865" alt="EmailCOnfig" src="https://github.com/user-attachments/assets/2c0602a3-bb93-4858-8df0-0296ab1015cb" />

Testing:
--------
<img width="1742" height="795" alt="EmailNotification_Test" src="https://github.com/user-attachments/assets/d34fc3f3-bc51-4746-9a4c-bd514f44f20d" />
<img width="695" height="432" alt="emailnotify" src="https://github.com/user-attachments/assets/876f02c2-f3ef-43fe-b845-1e16a3db1e6c" />

success:
----------
<img width="887" height="346" alt="SuccessMail" src="https://github.com/user-attachments/assets/87759a50-4027-4155-8cf9-41e8483271bb" />


6.Configure Github Webhook Triggers 

<img width="1522" height="536" alt="GitTriggered" src="https://github.com/user-attachments/assets/e3898985-24aa-4304-a8f4-58d91deae607" />

Triggers to Build Pipeline job:

<img width="1271" height="846" alt="gitpush_Triger" src="https://github.com/user-attachments/assets/63884d2c-f473-4b1b-8b64-9f40daadef63" />
<img width="1522" height="536" alt="GitTriggered" src="https://github.com/user-attachments/assets/08f8389c-ac9d-460d-8997-19017983f190" />

7.Flask Application
<img width="1700" height="506" alt="EC2_App5000" src="https://github.com/user-attachments/assets/d546a2ac-af74-4a1d-801d-143b5aaacaaf" />


🤖 Project 2: GitHub Actions Workflow
---------------------------------------------------------------
**Objective**
Implement a native GitHub CI/CD workflow using YAML configurations.

**Setup:**
1.Create Staging branch in Repo
<img width="1563" height="731" alt="A2_Branch" src="https://github.com/user-attachments/assets/6a7a097f-b89f-4635-b0c6-04099ba5ec9c" />

2.Create workflow.yml file in branch

<img width="1688" height="551" alt="A2_ActionsResult" src="https://github.com/user-attachments/assets/efb099d0-4dc1-415e-831d-0a69d8d5585e" />

<img width="1902" height="818" alt="A2_buildtest" src="https://github.com/user-attachments/assets/7adb26bb-20cb-42c8-af06-75d912ed6f55" />

3.Setup Staging & Production Environemnet Keys( Treated both Environment in EC2 INstance itself)

<img width="1361" height="958" alt="A2_Secrets" src="https://github.com/user-attachments/assets/35503010-fc9f-4c48-adc8-ad4e57923a5b" />

4.Trigger StagingDeployment by pushing changes in Staging branch

Push Changes:
<img width="1532" height="796" alt="A2_staging" src="https://github.com/user-attachments/assets/66d6e1f3-f2f3-419c-8c31-294a6d04119b" />

Check Actions Tab:
<img width="1026" height="643" alt="A2_stagingResult1" src="https://github.com/user-attachments/assets/86933b9d-6e71-463d-97ba-059b6940eaa1" />

<img width="1038" height="806" alt="Staging_BranchResult" src="https://github.com/user-attachments/assets/30ceebb7-23b9-43da-ac8a-1cf9a7e4a7fb" />

5.Trigger ProductionDeploymemnt by publishing release 

Publish Release:
<img width="1572" height="870" alt="Release1" src="https://github.com/user-attachments/assets/4e45226c-2d98-4f76-b07e-c9673fe6920d" />

Check ACtions Tab:
<img width="1146" height="507" alt="ReleaseTrigger" src="https://github.com/user-attachments/assets/1f193595-75ba-47d6-910b-eda141cc768f" />

<img width="1628" height="817" alt="GithubActions" src="https://github.com/user-attachments/assets/8e45f2aa-45ae-4940-85f5-d8b891851f3b" />
<img width="1250" height="720" alt="ProdResultA2" src="https://github.com/user-attachments/assets/df4a0965-95dc-4144-8e62-2d041e7a3cfa" />


6.Check in EC2
<img width="702" height="797" alt="A2_EC2Result" src="https://github.com/user-attachments/assets/14b868b9-c3f0-499b-a2c4-711c8c0a3159" />

