pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'git@github.com:bindukantharju/resubmissionrepo-banking-deployment12.git',
                        credentialsId: 'bindukantharju-key'
                    ]]
                ])
                sh 'ls -la'
            }
        }
    }
}








