node {
    def mavenCMD = 'mvn'
    def dockerCMD = 'docker'
    def tagName = "3.0"
    def imageName = "bindukantharaju/banking-app:${tagName}"

    stage('Checkout Code') {
        echo 'Cloning GitHub repo...'
        checkout([$class: 'GitSCM',
            branches: [[name: '*/main']],
            userRemoteConfigs: [[
                url: 'git@github.com:bindukantharju/resubmissionrepo-banking-deployment12.git',
                credentialsId: 'bindukantharju-key'
            ]]
        ])
        sh 'ls -la'
    }

    stage('Build with Maven') {
        echo "Building Java application..."
        sh "${mavenCMD} clean package"
    }

    stage('Publish Test Reports') {
        echo "Publishing test reports..."
        publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: false,
            reportDir: 'target/surefire-reports',
            reportFiles: 'index.html',
            reportName: 'Test Report'
        ])
    }

    stage('Docker Build') {
        echo "Building Docker image..."
        sh "${dockerCMD} build -t ${imageName} ."
    }

    stage('Docker Push') {
        echo "Pushing Docker image to DockerHub..."
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub-creds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh "${dockerCMD} login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
            sh "${dockerCMD} push ${imageName}"
        }
    }

    stage('Run Docker Container Locally') {
        echo "Running container locally on Jenkins host..."
        sh """
            ${dockerCMD} rm -f banking-app || true
            ${dockerCMD} run -d -p 8084:8081 --name banking-app ${imageName}
        """
    }
}







