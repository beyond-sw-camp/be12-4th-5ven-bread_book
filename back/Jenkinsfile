pipeline {
    agent none

    environment {
        IMAGE_NAME = 'alpaka1111/breadbook-be-jenkins'
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {
            stage('Build & Push') {
                agent { label 'build' }
                steps {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB_KHR', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'chmod +x gradlew'
                        sh './gradlew bootJar'
                        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                        sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                        sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }


        stage('Blue-Green Deploy') {
            agent { label 'deploy' }
            steps {
                script {
                    def color      = (BUILD_NUMBER.toInteger() % 2 == 0) ? 'green' : 'blue'
                    def otherColor = (color == 'green') ? 'blue' : 'green'
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'k8s',
                                verbose: true,
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'k8s/backend-deployment.yml, k8s/service.yml',
                                        remoteDirectory: '/backend',
                                        execCommand: """
                                            sed -i "s/LATEST/${BUILD_NUMBER}/g" /home/test/backend/k8s/backend-deployment.yml
                                            sed -i "s/COLOR/${color}/g" /home/test/backend/k8s/backend-deployment.yml
                                            sed -i "s/COLOR/${color}/g" /home/test/backend/k8s/service.yml
                                        """
                                    ),
                                    sshTransfer(
                                        execCommand: """
                                            export KUBECONFIG=/etc/kubernetes/admin.conf
                                            kubectl apply -f /home/test/backend/k8s/backend-deployment.yml
                                            kubectl rollout status deployment/backend-deployment-${color} -n breadbook --timeout=120s
                                            kubectl apply -f /home/test/backend/k8s/service.yml
                                            kubectl patch svc backend-svc -n breadbook -p '{\"spec\":{\"selector\":{\"deployment\":\"${color}\"}}}'
                                            kubectl scale deployment/backend-deployment-${otherColor} -n breadbook --replicas=0 || true
                                        """
                                    )
                                ]
                            )
                        ]
                    )

                }
            }
        }
    }
}
