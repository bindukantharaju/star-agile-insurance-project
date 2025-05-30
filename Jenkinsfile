pipeline {
    agent any

    stages {
        stage('Git Clone') {
            steps {
                git url: 'https://github.com/bindukantharaju/star-agile-insurance-project.git', branch: 'master'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t bindukantharaju/staragileprojectfinance:v1 ."
                sh "docker images"
            }
        }

        stage('Docker Login and Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                        docker push bindukantharaju/staragileprojectfinance:v1
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f my-First-container2211 || true
                    docker run -itd --name my-First-container2211 -p 8081:8081 bindukantharaju/staragileprojectfinance:v1

                '''
            }
        }
    }
}









