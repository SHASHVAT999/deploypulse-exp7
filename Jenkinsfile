pipeline {
    agent any

    environment {
        EXP_DIR = 'D:\\cloud-devops-assignment\\vle-pbl3\\exp7'
        APP_DIR = 'D:\\cloud-devops-assignment\\vle-pbl3\\exp7\\app'
        K8S_DIR = 'D:\\cloud-devops-assignment\\vle-pbl3\\exp7\\k8s'

        KUBECONFIG = 'D:\\cloud-devops-assignment\\vle-pbl3\\shared\\kube\\config'

        DOCKER_EXE = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
        KUBECTL_EXE = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe'
        KIND_EXE = 'D:\\cloud-devops-assignment\\vle-pbl3\\tools\\bin\\kind.exe'
        GIT_EXE = 'D:\\Git\\cmd\\git.exe'

        IMAGE = 'exp7-dashboard:1.0'
    }

    stages {
        stage('Source Control') {
            steps {
                echo 'Checking Git repository...'
                bat '"%GIT_EXE%" -C "%EXP_DIR%" log -1 --oneline'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat '"%DOCKER_EXE%" build -t "%IMAGE%" "%APP_DIR%"'
            }
        }

        stage('Load Image into kind') {
            steps {
                echo 'Loading image into Kubernetes cluster...'
                bat '"%KIND_EXE%" load docker-image "%IMAGE%" --name exp7-cluster'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Applying Kubernetes manifests...'
                bat '''
                "%KUBECTL_EXE%" apply -f "%K8S_DIR%\\namespace.yaml"
                "%KUBECTL_EXE%" apply -f "%K8S_DIR%\\deployment.yaml"
                "%KUBECTL_EXE%" apply -f "%K8S_DIR%\\service.yaml"
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Waiting for Kubernetes rollout...'
                bat '"%KUBECTL_EXE%" rollout status deployment/exp7-dashboard -n exp7 --timeout=120s'

                echo 'Checking running pods...'
                bat '"%KUBECTL_EXE%" get pods -n exp7 -o wide'

                echo 'Checking Kubernetes service...'
                bat '"%KUBECTL_EXE%" get svc -n exp7'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: DeployPulse deployed successfully!'
        }
        failure {
            echo 'FAILED: Check the Jenkins console output.'
        }
    }
}
