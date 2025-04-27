pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'GITHUB_AUTH',
                    url: 'https://github.com/fatimacisse/spring-boot-ci-cd.git',
                    branch: 'develop'
            }
        }
    }
}

