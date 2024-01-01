## Setting Up Jenkins Conditional Pipeline for xFusionCorp Industries

### Task Objective and Provided Requirements:
The goal is to Develop a Jenkins pipeline ('nautilus-webapp-job') to deploy a new static website onto Nautilus App Servers based on the provided requirements from the xFusionCorp Industries' development team.

### Provided Requirements:
1. Access Jenkins UI using admin credentials (Username: admin, Password: Adm!n321).
2. Access Gitea UI using sarah's credentials (Username: sarah, Password: Sarah_pass123).
3. Locate 'web_app' repository under user sarah, already cloned on the Storage server (/var/www/html).
4. Add a Storage Server Slave Node labeled as ststor01 with the remote root directory as /var/www/html.
5. The pipeline ('nautilus-webapp-job') must not be a Multibranch pipeline.
6. Configure a 'BRANCH' string parameter in the pipeline.
7. Conditionally deploy the code from the web_app repository under /var/www/html on the Storage Server.
8. Use a single case-sensitive stage named 'Deploy' to accomplish the deployment.
9. Conditionally deploy the 'master' branch if 'master' is passed to the BRANCH parameter; deploy the 'feature' branch if 'feature' is passed.
10. Ensure the main URL (https://<LBR-URL>) does not contain a sub-directory, displaying the required content.

### Solution:
1. **I Installed The Necessary Plugins on Jenkins:**

Install required plugins: SSH, Git, and Pipeline.
![image1](./images/dockerimagetransfer1.png)

2. **I Added The Slave Node 'Storage Server':**

- I Ensured Java is installed on the server for adding a slave node.
- Add a slave node under Jenkins management:
  - Label: ststor01
  - Remote Root Path: /var/www/html
- I Added permissions by changing ownership of /var/www/html to the server user `chown -R natasha /var/www/html .





3. **I Created 'nautilus-webapp-job' Pipeline For The Job:**

- I Created a pipeline named 'nautilus-webapp-job'.
- I Added a string parameter named 'BRANCH'.
- Configured the Branch to build as ./$BRANCH, based on the value passed before the job runs.


4. **I Wrote The Pipeline Script:**

```pipeline {
    agent none

    stages {
        stage('Deploy') {
            agent {
                label "ststor01"
            }
            steps {
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/$BRANCH']],
                    userRemoteConfigs: [[url: 'http://git.stratos.xfusioncorp.com/sarah/web_app.git']]
                ]
                sh 'cp -r /var/www/html/workspace/nautilus-webapp-job/* /var/www/html'
                // Additional steps...
            }
        }
    }
}
```


 5. **I Executed and Tested the Job:**
   - Ran the job with 'master' as the value for BRANCH parameter.
   - Successfully completed the job, fetching content from the Git repository and displaying it on the website.
