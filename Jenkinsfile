pipeline {
    agent any
    
    environment {
        DOCKER_ID = "mhunault" // replace this with your docker-id
    }

    stages {
        stage('Build and Test') {
            steps {
                script {
                    // Étape de construction et de test pour cast-service
                    dir('cast-service') {
                        sh 'docker build -t cast-service:latest .'
                        sh 'docker run cast-service:latest python -m pytest tests/'
                    }
                    
                    // Étape de construction et de test pour movie-service
                    dir('movie-service') {
                        sh 'docker build -t movie-service:latest .'
                        sh 'docker run movie-service:latest python -m pytest tests/'
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Déploiement dans Kubernetes pour cast-service
                    dir('repo_github/kubernetes/cast-service') {
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                    }

                    // Déploiement dans Kubernetes pour movie-service
                    dir('repo_github/kubernetes/movie-service') {
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Connexion à DockerHub
                    sh "docker login -u DOCKER_ID -p DOCKER_HUB_PASS"

                    // Pousser les images vers DockerHub
                    sh 'docker push cast-service:latest'
                    sh 'docker push movie-service:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'Build, Deployment, and Push to DockerHub Successful!'
        }
    }
}
