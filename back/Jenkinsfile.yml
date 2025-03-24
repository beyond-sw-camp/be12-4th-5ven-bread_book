pipeline {
    agent none

    environment {
        IMAGE_NAME = 'wkdlrn/breadbookback'
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {
            stage('Build & Push') {
                agent { label 'build' }
                steps {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
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

                    sh """
ssh test@192.0.5.9 "export KUBECONFIG=/etc/kubernetes/admin.conf && kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  namespace: breadbook
  name: backend-svc
spec:
  selector:
    type: backend
    deployment: ${color}
  ports:
    - port: 8080
      targetPort: 8080
  type: ClusterIP
EOF"
"""
                    sh """
ssh test@192.0.5.9 "export KUBECONFIG=/etc/kubernetes/admin.conf && kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: breadbook
  name: backend-deployment-${color}
spec:
  replicas: 2
  selector:
    matchLabels:
      type: backend
      deployment: ${color}
  template:
    metadata:
      labels:
        type: backend
        deployment: ${color}
    spec:
      containers:
      - name: backend-${color}
        image: ${IMAGE_NAME}:${IMAGE_TAG}
        ports:
           - containerPort: 8080
        volumeMounts:
        - name: image-upload
          mountPath: /mnt
        envFrom:
        - configMapRef:
            name: back-cm
      volumes:
        - name: image-upload
          persistentVolumeClaim:
            claimName: image-pvc
EOF"
"""
                    sh "ssh test@192.0.5.9 \"kubectl rollout status deployment/backend-deployment-${color} -n breadbook --timeout=120s\""
                    sh "ssh test@192.0.5.9 \"kubectl patch svc backend-svc -n breadbook -p '{\\\"spec\\\":{\\\"selector\\\":{\\\"deployment\\\":\\\"${color}\\\"}}}'\""
                    sh "ssh test@192.0.5.9 \"kubectl scale deployment/backend-deployment-${otherColor} -n breadbook --replicas=0 || true\""
                }
            }
        }
    }
}
