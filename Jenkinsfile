pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'manoj1812/two-tier-springboot'
        DOCKER_CREDENTIALS = 'dockerhub-creds'
        AWS_REGION = 'us-east-1'
        EKS_CLUSTER = 'two-tier-eks'
        K8S_NAMESPACE = 'production'
        DEPLOYMENT = 'two-tier-app'
        CONTAINER = 'two-tier-app'
    }

    stages {
 
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

       stage('Build & Test') {
    steps {
        sh '''
            chmod +x mvnw

            export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
            export PATH=$JAVA_HOME/bin:$PATH

            echo "Java used by Maven:"
            java -version

            echo "Maven Java:"
            ./mvnw -version

            ./mvnw clean package
        '''
    }
}

        stage('Docker Build') {
            steps {
                script {
                    env.IMAGE_TAG = "build-${env.BUILD_NUMBER}"

                    sh """
                        docker build \
                          -t ${DOCKER_IMAGE}:${IMAGE_TAG} \
                          -t ${DOCKER_IMAGE}:latest .
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                          -u "$DOCKER_USER" \
                          --password-stdin

                        docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                        docker push ${DOCKER_IMAGE}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
    steps {
        sh '''
            aws eks update-kubeconfig \
              --region ${AWS_REGION} \
              --name ${EKS_CLUSTER}

            echo "Deploying image: ${DOCKER_IMAGE}:${IMAGE_TAG}"

            kubectl -n ${K8S_NAMESPACE} set image deployment/${DEPLOYMENT} \
              ${CONTAINER}=${DOCKER_IMAGE}:${IMAGE_TAG}
        '''
    }
}

       stage('Verify Deployment') {
    steps {
        script {
            try {
                sh '''
                    kubectl rollout status \
                      deployment/${DEPLOYMENT} \
                      -n ${K8S_NAMESPACE} \
                      --timeout=5m
                '''

                sh '''
                    kubectl get deployment ${DEPLOYMENT} \
                      -n ${K8S_NAMESPACE}

                    kubectl get pods \
                      -n ${K8S_NAMESPACE}
                '''

            } catch (Exception e) {

                echo "Deployment failed!"
                echo "Starting automatic rollback..."

                sh '''
                    kubectl rollout undo deployment/${DEPLOYMENT} \
                      -n ${K8S_NAMESPACE}

                    kubectl rollout status \
                      deployment/${DEPLOYMENT} \
                      -n ${K8S_NAMESPACE} \
                      --timeout=5m
                '''

                error("Deployment failed. Automatic rollback completed.")
            }
        }
    }
}

    post {
        success {
            echo "CI/CD Pipeline completed successfully!"
        }

        failure {
            echo "CI/CD Pipeline failed. Check the stage logs."
        }
    }
}
