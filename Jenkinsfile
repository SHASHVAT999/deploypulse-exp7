pipeline {
    agent any

    environment {
        EXP_DIR = 'D:\\cloud-devops-assignment\\vle-pbl3\\exp7'
        KUBECONFIG = 'D:\\cloud-devops-assignment\\vle-pbl3\\shared\\kube\\config'

        DOCKER_EXE = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
        KUBECTL_EXE = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe'
        KIND_EXE = 'D:\\cloud-devops-assignment\\vle-pbl3\\tools\\bin\\kind.exe'
        GIT_EXE = 'D:\\Git\\cmd\\git.exe'

        IMAGE = 'exp7-dashboard:1.0'
    }

    stages {
        stage('Git Checkout') {
            steps {
                deleteDir()
                echo 'Cloning application from local Git repository...'
                bat '"%GIT_EXE%" clone --no-hardlinks "%EXP_DIR%" "%WORKSPACE%\\source"'
                bat '"%GIT_EXE%" -C "%WORKSPACE%\\source" log -1 --oneline'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                bat '"%DOCKER_EXE%" build -t "%IMAGE%" "%WORKSPACE%\\source\\app"'
            }
        }

        stage('Load Image into Kubernetes') {
            steps {
                echo 'Loading Docker image into kind...'
                bat '"%KIND_EXE%" load docker-image "%IMAGE%" --name exp7-cluster'
            }
        }

        stage('Kubernetes Deployment') {
            steps {
                echo 'Creating namespace...'
                bat '"%KUBECTL_EXE%" apply -f "%WORKSPACE%\\source\\k8s\\namespace.yaml"'

                echo 'Deploying application...'
                bat '"%KUBECTL_EXE%" apply -f "%WORKSPACE%\\source\\k8s\\deployment.yaml"'
                bat '"%KUBECTL_EXE%" apply -f "%WORKSPACE%\\source\\k8s\\service.yaml"'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '"%KUBECTL_EXE%" rollout status deployment/exp7-dashboard -n exp7 --timeout=120s'
                bat '"%KUBECTL_EXE%" get pods -n exp7 -o wide'
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
