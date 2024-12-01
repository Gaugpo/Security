pipeline {
    agent any

    tools {
        jdk 'JDK 11' // ou 'JDK 20.0.2' selon votre configuration
        maven 'Maven 3.8.1'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'OWASP Dependency-Check'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube Server') { // Assurez-vous que le nom correspond à votre configuration
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') { // Attendre le résultat de la qualité
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }

        stage('Checkmarx Scan') {
            steps {
                // Remplacez par votre commande Checkmarx
                script {
                    // Exemple de commande Checkmarx
                    sh 'checkmarx-scan-command --project "YourProjectName" --source "src"'
                }
            }
        }

        stage('SpotBugs Analysis') {
            steps {
                sh 'mvn spotbugs:check'
            }
        }

        stage('PMD Analysis') {
            steps {
                sh 'mvn pmd:pmd'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
