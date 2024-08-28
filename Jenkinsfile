pipeline {
    agent any

    stages {
        stage('Login and Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nahla-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "Logging in with user: $USERNAME"
                        echo $PASSWORD | docker login -u "$USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:alpine'
                }
            }
            steps {
                sh '''
                    echo "With docker"
                    echo "Nahla" > nahla.txt
                    node --version
                '''
                stash name: 'myname-file', includes: 'nahla.txt'
            }
        }

        stage('Deploy Nginx Alpine Container') {
            steps {
                sh 'docker pull nginx:alpine'
                sh '''
                    docker run -d \
                    --name my-nginx-alpine \
                    -p 6000:80 \
                    nginx:alpine
                '''
            }
        }
    }

    post {
        always {
            unstash 'myname-file'
            sh '''
                docker stop my-nginx-alpine
                docker rm my-nginx-alpine 
                cat nahla.txt
            '''
        }
    }
}
