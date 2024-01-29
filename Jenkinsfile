pipeline {
    agent any
    environment {
	DOCKER_ID = 'mhunault'
	DOCKER_TAG = "v.${BUILD_ID}.0"
    GITHUB_REPO = "dt_jenkins_exam"
	DOCKER_IMAGE = "dt_jenkins_exams"
    }
    stages {
        stage('Build and Test') {
            steps {
                script {
                    dir('cast-service') {
                        sh 'docker build -t cast-service:latest .'
                        sh 'docker run cast-service:latest'
                    }
                    dir('movie-service') {
                        sh 'docker build -t movie-service:latest .'
                        sh 'docker run movie-service:latest'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
		            sh 'ls repo_github/kubernetes/cast-service'
                    dir('GITHUB_REPO/kubernetes/cast-service') {
                        //sh 'kubectl apply -f deployment.yaml'
                        //sh 'kubectl apply -f service.yaml'
                    }
                    dir('GITHUB_REPO/kubernetes/movie-service') {
                        //sh 'kubectl apply -f deployment.yaml'
                        //sh 'kubectl apply -f service.yaml'
                    }
                }
            }
        }
        stage('Push to DockerHub') {
            environment {
			    DOCKER_HUB_USR = credentials("DOCKER_HUB_USR")
                DOCKER_HUB_PASS = credentials("DOCKER_HUB_PASS")
            }
            steps {
                script {
			        sh 'echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USR --password-stdin || true'
            		sh 'docker push $DOCKER_HUB_USR/movie-service'
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
