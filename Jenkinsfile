pipeline {
    agent { label 'k8s-server' }
    stages {
        stage('Pull Code From GitHub') {
            steps {
                git 'https://github.com/kmbharathi97/ball-game.git'
            }
        }
        stage('Build the Docker image') {
            steps {
                sh 'sudo docker build -t ballimage /root/project/workspace/k8s-game'
                sh 'sudo docker tag ballimage kmbharathi97/ballimage:latest'
                sh 'sudo docker tag ballimage kmbharathi97/ballimage:${BUILD_NUMBER}'
            }
        }
        stage('Trivy Vulnerability Scan') {
            steps {
                script {
                    sh 'sudo trivy image kmbharathi97/ballimage:latest'
                    sh 'sudo trivy image kmbharathi97/ballimage:${BUILD_NUMBER}'
                }
            }
        }
        stage('Push the Docker image') {
            steps {
                sh 'sudo docker image push kmbharathi97/ballimage:latest'
                sh 'sudo docker image push kmbharathi97/ballimage:${BUILD_NUMBER}'
            }
        }
        stage('Deploy on Kubernetes') {
            steps {
                sh 'sudo kubectl apply -f /root/project/workspace/k8s-game/pod.yml'
                sh 'sudo kubectl rollout restart deployment loadbalancer-pod'
            }
        }
    }
}
