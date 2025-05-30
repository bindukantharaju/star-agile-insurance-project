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
                sh 'sudo docker build -t bindukantharaju/staragileprojectfinance:v1 .'
                sh 'sudo docker images'
            }
        }

        stage('Docker Login and Push') {
            steps {
                withCredentials([string(credentialsId: 'dockerhubpass', variable: 'DOCKERHUB_PASS')]) {
                    sh '''
                        set +x
                        echo "$DOCKERHUB_PASS" | sudo docker login -u bindukantharaju --password-stdin
                        set -x
                        sudo docker push bindukantharaju/staragileprojectfinance:v1
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    sudo docker rm -f my-container || true
                    sudo docker run -itd --name my-container -p 8081:8081 bindukantharaju/staragileprojectfinance:v1
                '''
            }
        }
    }
}









