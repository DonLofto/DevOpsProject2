pipeline {

    agent any


    environment {
        WORKSPACE_DIR = "build_workspace_${env.BUILD_ID}" // The workspace directory for the build.
        DOCKER_IMAGE_TAG = "latest" // The Docker image tag to be used.

    }


    stages {
        // This stage prepares the workspace for the build.
        stage('Prepare Workspace') {
            steps {
                sh 'mkdir -p ${WORKSPACE_DIR}'
            }
        }


        stage('Checkout') {
            steps {
                sh 'git clone https://github.com/DonLofto/DevOpsProject.git ${WORKSPACE_DIR}'
            }
        }


        stage('Run docker compose') {
            steps {
                dir("${WORKSPACE_DIR}") {
                    script {
                        input message: 'Deploy to production?', ok: 'Deploy'
                        sh 'docker-compose down -v --remove-orphans'
                        sh 'docker-compose up -d'
                    }
                }
            }
        }
    }

    // The post section runs after the pipeline's run and is usually used for clean up and notification tasks.
    post {
        // The success condition runs if the pipeline completed successfully.
        success {
            echo 'Deployment successful!'
            // The emailext step sends an email depending on the status of the pipeline.
            emailext(
                subject: 'Jenkins Notification - Deployment Successful',
                body: 'Deployment of your application was successful.',
                to: "${EMAIL_RECIPIENT}"
            )
            script {
                sh 'docker image prune -f'
            }
        }
        // The always condition runs regardless of the completion status of the pipeline.
        always {
            sh "rm -rf ${WORKSPACE_DIR}"
        }
    }
}