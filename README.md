# Jenkins Declarative Pipeline Examples

This document provides examples of Jenkins Declarative Pipelines for different scenarios. 

## Simple Pipeline Example

### Pipeline Code

```groovy
pipeline {
    agent any
    
    environment{
        NAME = "Aditya"
        AGE = 23
        PASS = credentials('PASS')
    }

    stages {
        stage('Build') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Test') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo Hello $NAME $PASS'
            }
        }
        stage('Custom Command') {
            steps {
                sh 'echo Hello'
            }
        }
        stage('Multipline Command') {
            steps {
                sh '''
                    echo Hello
                    echo Buddy
                '''
            }
        }
        stage('Retry') {
            steps {
                sh '''
                    echo retry
                '''
                retry(3){
                    sh 'echo trying...'
                }
            }
        }
        stage('Timeout') {
            steps {
                sh '''
                    echo timeout
                '''
                retry(3){
                    sh 'echo trying...'
                }
                
                timeout(time:15, unit: 'SECONDS'){
                    sh 'sleep 30'
                }
            }
        }
    }
}
```

### Features Explained

1. **Agent Declaration**:
   - `agent any` indicates that the pipeline can run on any available agent.

2. **Environment Variables**:
   - `NAME` and `AGE` are simple environment variables.
   - `PASS` uses Jenkins credentials with the ID `PASS`.

3. **Stages**:
   - **Build**: Prints "Hello World".
   - **Test**: Prints "Hello World".
   - **Deploy**: Uses `sh` to print environment variables.
   - **Custom Command**: Runs a single shell command.
   - **Multiline Command**: Demonstrates running multiple shell commands in a single block.
   - **Retry**: Retries a step up to 3 times if it fails.
   - **Timeout**: Sets a timeout of 15 seconds for a step to complete; otherwise, the step fails.

4. **Retry**:
   - The `retry(3)` block retries the command inside it up to 3 times in case of failure.

5. **Timeout**:
   - The `timeout` block ensures that the `sleep 30` command will fail if it takes longer than 15 seconds.

## GitHub + Maven Pipeline Example

### Pipeline Code

```groovy
pipeline {
    agent any

    tools {
        maven "jenkins-maven"
    }

    stages {
        stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/jenkins-docs/simple-java-maven-app.git'
            }
        }
        stage('Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Test'){
            steps{
                sh 'mvn test'
            }
            post{
                always{
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
        stage('Approval'){
            steps{
                input 'Are you sure to deploy in production?'
            }
        }
        stage('Deploy'){
            steps{
                sh 'java -jar target/*.jar'
            }
        }
    }

    post{
        failure{
            echo 'Failure!'
            mail to: 'adityakalambe20@gmail.com',
                 subject: "FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                 body: "job '${env.JOB_NAME}' (${env.BUILD_URL}) failed"
        }
        success{
            echo 'Build and test successful!'
            archiveArtifacts artifacts: '**/target/*.jar', onlyIfSuccessful: true
        }
    }
}
```

### Features Explained

1. **Tools Section**:
   - Specifies `maven` with a custom tool name `jenkins-maven` configured in Jenkins.

2. **Stages**:
   - **Checkout**: Clones a GitHub repository.
   - **Build**: Runs `mvn clean package` to build the application.
   - **Test**: Executes tests using `mvn test` and publishes test results with `junit`.
   - **Approval**: Prompts for manual approval before deploying to production.
   - **Deploy**: Deploys the application by running the built JAR file.

3. **Post Actions**:
   - **Failure**: Sends an email notification if the pipeline fails.
   - **Success**: Archives the built artifact if the pipeline is successful.

### Usage

- Ensure you have a Maven tool named `jenkins-maven` configured in Jenkins.
- Replace the GitHub repository URL with your own if needed.
- Replace the email address in the `mail` step with the desired recipient.
- Copy and paste the pipeline code into a Jenkins pipeline job.

## Node.js Pipeline Example

### Pipeline Code

```groovy
pipeline {
    agent any
    environment {
        CI = 'true'
    }
    tools{
        nodejs 'node-14'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/dinushchathurya/jenkins-nodejs-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Permissions'){
            steps{
                sh 'chmod +x jenkins/scripts/*.sh'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
```

### Features Explained

1. **Tools Section**:
   - Specifies `nodejs` with the tool name `node-14` configured in Jenkins.

2. **Stages**:
   - **Checkout**: Checks out the code from a GitHub repository.
   - **Build**: Installs Node.js dependencies using `npm install`.
   - **Permissions**: Grants executable permissions to scripts in `jenkins/scripts/`.
   - **Test**: Executes the `test.sh` script for testing.
   - **Deliver**: Runs deployment scripts and includes a manual input for confirmation before shutting down.

3. **Environment Variables**:
   - Sets `CI` to `true`, which may be used in scripts to indicate a CI environment.

### Usage

- Ensure Node.js is configured in Jenkins with the tool name `node-14`.
- Replace the repository URL and script paths as needed.
- Customize stages and scripts based on your application requirements.

---

This document now includes examples for Simple Pipelines, GitHub + Maven Pipelines, and Node.js Pipelines. Let me know if you need further enhancements!
