pipeline {
    agent any

    environment {
        IMAGE     = "bhawanaraj/myimage"
        CONTAINER = "myapp"
    }

    stages {
        stage('Pull Code from GitHub') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker push $IMAGE:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy on Localhost') {
            steps {
                sh '''
                    docker rm -f $CONTAINER || true
                    docker pull $IMAGE:latest
                    docker run -d --name $CONTAINER -p 8085:80 $IMAGE:latest
                '''
            }
        }
    }
}
