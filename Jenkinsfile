pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'echo "Executando o comando Docker Build!"'
                script {
                    dockerapp = docker.build("adrianoagiljr/teste:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'echo "Executando o comando Docker Push!"'
                script {
                    docker.withRegistry('', 'dockerhub') {
                        dockerapp.push()
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            environment{
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                sh 'echo "Executando o comando Kubectl Apply!"'

                withKubeConfig([credentialsId: 'kubeconfig']){
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}