pipeline {
    agent any

    tools {
        jdk 'jdk11' // Assurez-vous que JDK 11 est installé et configuré
        maven 'maven3' // Assurez-vous que Maven est installé et configuré
    }

    stages {
        stage('Checkout') {
            steps {
                // Utiliser checkout scm pour récupérer le code source
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Compiler le projet avec Maven
                sh 'mvn clean package'
            }
        }

        stage('Unit Tests') {
            steps {
                // Exécuter les tests unitaires
                sh 'mvn test'
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                // Exécuter OWASP Dependency-Check pour détecter les vulnérabilités
                sh 'mvn dependency-check:check'
            }
        }

        stage('Publish OWASP Dependency Check Report') {
            steps {
                // Publier le rapport OWASP Dependency Check
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'target',
                    reportFiles: 'dependency-check-report.html',
                    reportName: 'OWASP Dependency Check Report'
                ])
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Analyser le code avec SonarQube pour détecter les problèmes de qualité et de sécurité
                withSonarQubeEnv('SonarQube') { // Assurez-vous que SonarQube est configuré dans Jenkins
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Security Code Scan') {
            steps {
                // Utiliser SpotBugs pour vérifier les vulnérabilités dans le code
                sh 'mvn com.github.spotbugs:spotbugs-maven-plugin:check'
            }
        }

        stage('Deploy') {
            steps {
                // Déployer l'application (par exemple, copier le fichier JAR sur un serveur)
                sh 'scp target/UserService.jar user@server:/path/to/deploy/'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
