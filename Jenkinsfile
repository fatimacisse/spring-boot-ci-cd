pipeline {
    agent {
        docker {
            image 'maven:3.8.4-jdk-11' // Agent Docker avec Maven et JDK 11
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Accès au socket Docker
        }
    }
    environment {
        SONAR_TOKEN = credentials('JENKINS_TOKEN_SONAR') // Token SonarCloud (exemple)
        SLACK_WEBHOOK = credentials('TOKEN_SLACK') // URL webhook Slack (exemple)
 	DOCKERHUB_AUTH = credentials('DOCKERHUB_AUTH') // Credential DockerHub combiné
	BRANCH_NAME = develop
 	
    }
    stages {
        stage('Tests Automatisés') {
            steps {
                script {
                    // Gestion des branches selon Gitflow
                  //  if (BRANCH_NAME == 'main' || BRANCH_NAME == 'develop') {
                        echo "Branche actuelle : ${BRANCH_NAME}"
                        echo 'Exécution des tests unitaires...'
                        sh 'mvn test' // Tests unitaires

                        echo 'Exécution des tests d\'intégration...'
                        sh 'mvn verify' // Tests d'intégration
                   // } else {
                        echo "Tests Automatisés exécutés uniquement pour les branches main et develop."
                        echo "Branche actuelle : ${BRANCH_NAME}"
                   // }
                }
            }
        }
	stage('Vérification de la Qualité de Code') {
            steps {
                script {
                   // if (BRANCH_NAME == 'main' || BRANCH_NAME == 'develop') {
                        echo "Branche actuelle : ${BRANCH_NAME}"
                        echo 'Analyse statique du code avec SonarCloud (en utilisant sonar-project.properties)...'
                        sh """
                            mvn sonar:sonar \
                            -Dsonar.login=$SONAR_TOKEN
                        """
                   // } else {
                        echo "L'analyse de Qualité de Code n'est exécutée que pour les branches main et develop."
                   // }
                }
            }
        }
	stage('Compilation et Packaging') {
            steps {
                script {
               	   // if (BRANCH_NAME == 'main' || BRANCH_NAME == 'develop') {
                        echo "Branche actuelle : ${BRANCH_NAME}"
                        echo 'Compilation et génération du package...'
                        sh 'mvn clean package' // Fichier JAR/WAR

                        echo 'Création de l\'image Docker...'
                        sh """
                            docker build -t $DOCKERHUB_AUTH_USR/spring-boot-ci-cd:${BRANCH_NAME} .
                        """

                        echo 'Connexion à DockerHub...'
                        sh """
                            echo $DOCKERHUB_AUTH | docker login --username $DOCKERHUB_AUTH_USR --password-stdin
                        """

                        echo 'Envoi de l\'image Docker vers DockerHub...'
                        sh """
                            docker push $DOCKERHUB_AUTH_USR/spring-boot-ci-cd:${BRANCH_NAME}
                        """
                   // } else {
                        echo "Compilation et Packaging exécutés uniquement pour les branches main et develop."
                   // }
                }
            }
        }
    }
}

