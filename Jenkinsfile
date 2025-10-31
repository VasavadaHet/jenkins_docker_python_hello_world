pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'hello-world-python:1.0'
        HOST_PORT = '8081'
        CONTAINER_PORT = '8000'
        CONTAINER_NAME = 'hello-app' // Use a variable for the container name
    }

    stages {
        stage('Hello & Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/VasavadaHet/jenkins_docker_python_hello_world'
                echo 'Source code checked out.'
            }
        }
        
        stage('Docker Build') {
            steps {
                script {
                    if (fileExists('Dockerfile')) {
                        sh "docker build -t ${env.DOCKER_IMAGE} ."
                    } else {
                        error "Dockerfile not found in the workspace"
                    }
                }
            }
        }
        
        stage('Docker Run & Port Expose') {
            steps {
                echo "Running Docker image: ${env.DOCKER_IMAGE}"
                
                // Start the container in detached mode (-d) with port mapping (-p)
                // and assign a name (--name)
                sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:${env.CONTAINER_PORT} ${env.DOCKER_IMAGE}"
                
                echo "Application is now accessible on http://<Jenkins-Host-IP>:${env.HOST_PORT}"
            }
        }
    }

    // POST: Executes actions after all stages have run.
    post {
        // Runs only if the entire pipeline finished successfully (all stages passed).
        success {
            echo "================================================="
            echo "PIIPELINE COMPLETED SUCCESSFULLY!"
            echo "Access app on http://<Jenkins-Host-IP>:${env.HOST_PORT}"
            echo "================================================="
        }
        
        // Runs regardless of whether the pipeline succeeded or failed.
        // This is the ideal place for cleanup actions.
        always {
            // Stop and remove the container if it exists, using '|| true' to prevent
            // the pipeline from failing if the container wasn't started (e.g., due to a build failure)
            sh "docker stop ${env.CONTAINER_NAME} || true"
            sh "docker rm ${env.CONTAINER_NAME} || true"
            echo 'Docker container stopped and removed (if it was running).'
        }
        
        // You could also add a 'failure' block for error reporting (e.g., sending an email)
        failure {
            echo "Pipeline failed at one of the stages."
        }
    }
}
