node {
    
    def mavenHome
    def mavenCMD
    def docker
    def dockerCMD
    def tagName

    stage('Prepare Environment') {
        echo 'Initializing all the variables'
        mavenHome = tool name: 'maven', type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        docker = tool name: 'docker', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        dockerCMD = "${docker}/bin/docker"
        tagName = "v${env.BUILD_NUMBER}"
    }

    stage('Git Code Checkout') {
        try {
            echo 'Checking out the code from Git repository (using SSH key)'
            checkout([$class: 'GitSCM',
                branches: [[name: '*/main']],
                userRemoteConfigs: [[
                    url: 'git@github.com:bindukantharju/resubmissionrepo-banking-deployment12.git',
                    credentialsId: 'bindukantharju-key' // Jenkins credential for SSH private key
                ]]
            ])
        } catch (Exception e) {
            echo 'Exception occurred in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
            emailext body: '''Dear All,
            The Jenkins job ${JOB_NAME} has failed. Please check it immediately at:
            ${BUILD_URL}''', 
            subject: 'Job ${JOB_NAME} ${BUILD_NUMBER} FAILED', 
            to: 'bindukantharju@gmail.com'
        }
    }

    stage('Build the Application') {
        echo "Cleaning... Compiling... Testing... Packaging..."
        sh "${mavenCMD} clean package"
    }

    stage('Publish Test Reports') {
        publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: false,
            reportDir: 'target/surefire-reports',
            reportFiles: 'index.html',
            reportName: 'HTML Report',
            useWrapperFileDirectly: true
        ])
    }

    stage('Containerize the Application') {
        echo 'Creating Docker image'
        sh "${dockerCMD} build -t bindukantharaju/banking-app:${tagName} ."
    }

    stage('Push to DockerHub') {
        echo 'Pushing Docker image to DockerHub...'
        withCredentials([string(credentialsId: 'dockerhub-creds', variable: 'dockerHubPassword')]) {
            sh "${dockerCMD} login -u bindukantharaju -p ${dockerHubPassword}"
            sh "${dockerCMD} push bindukantharaju/banking-app:${tagName}"
        }
    }

    stage('Configure and Deploy to the Test Server') {
        echo 'Deploying using Ansible...'
        ansiblePlaybook(
            become: true,
            credentialsId: 'ansible-key',
            disableHostKeyChecking: true,
            installation: 'ansible',
            inventory: '/etc/ansible/hosts',
            playbook: 'ansible-playbook.yml'
        )
    }
}




