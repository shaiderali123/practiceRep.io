```groovy
pipeline {

    agent any

    parameters {
        choice(
            name: 'GIT_PROTOCOL',
            choices: ['HTTPS', 'SSH'],
            description: 'Select how Jenkins should access GitHub'
        )
    }

    environment {
        HTTPS_CREDENTIALS_ID = 'github-https'
        SSH_CREDENTIALS_ID   = 'github-ssh'

        GITHUB_HTTPS_URL = 'https://github.com/shaiderali123/practiceRep.io'
        GITHUB_SSH_URL   = 'git@github.com:shaiderali123/practiceRep.io'

        DEPLOY_DIR = '/tmp/jenkins-deployment'
    }

    stages {

        stage('Checkout') {
            steps {

                script {

                    if (params.GIT_PROTOCOL == 'HTTPS') {

                        echo 'Checking out repository using HTTPS...'

                        git(
                            branch: 'main',
                            credentialsId: env.HTTPS_CREDENTIALS_ID,
                            url: env.GITHUB_HTTPS_URL
                        )

                    } else {

                        echo 'Checking out repository using SSH...'

                        git(
                            branch: 'main',
                            credentialsId: env.SSH_CREDENTIALS_ID,
                            url: env.GITHUB_SSH_URL
                        )
                    }
                }
            }
        }

        stage('Test') {
            steps {

                echo 'Running tests...'

                sh '''
                    echo "Current user:"
                    whoami

                    echo "Current directory:"
                    pwd

                    echo "Git version:"
                    git --version
                '''
            }
        }

        stage('Build') {
            steps {

                echo 'Building project...'

                sh '''
                    mkdir -p build

                    cp -r . build/ 2>/dev/null || true

                    echo "Build completed."
                '''
            }
        }

        stage('Deploy with rsync') {
            steps {

                echo 'Deploying files using rsync...'

                sh '''
                    mkdir -p "$DEPLOY_DIR"

                    rsync -av --delete \
                        --exclude=".git" \
                        --exclude="build" \
                        ./ "$DEPLOY_DIR/"

                    echo "Deployment completed."
                '''
            }
        }
    }

    post {

        success {
            echo '=========================================='
            echo ' Jenkins Pipeline Completed Successfully '
            echo '=========================================='
        }

        failure {
            echo '=========================================='
            echo ' Jenkins Pipeline Failed '
            echo '=========================================='
        }

        always {
            echo 'Cleaning workspace...'
            cleanWs()
        }
    }
}
```
