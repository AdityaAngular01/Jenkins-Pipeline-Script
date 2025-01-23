# Jenkins Declarative Pipeline Example

This is an example of a Jenkins Declarative Pipeline that demonstrates various features such as environment variables, stages, retries, and timeouts.

## Pipeline Code

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

## Features Explained

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

## Usage

- Copy and paste the pipeline code into a Jenkins pipeline job.
- Replace `credentials('PASS')` with the appropriate credentials ID from your Jenkins instance.
- Customize the stages and commands as needed.

## Notes

- Ensure that Jenkins has the appropriate plugins installed (e.g., Pipeline plugin).
- Test the pipeline in a development environment before deploying it in production.

Enjoy using Jenkins pipelines!
