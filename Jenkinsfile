pipeline {
    agent any

    tools {
        jdk 'JDK 11'
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

    /*    stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '/target/surefire-reports/.xml'
                }
            }
        }
*/
        stage('SpotBugs Analysis') {
            steps {
                sh 'mvn spotbugs:check'
            }
            post {
                always {
                    recordIssues(tools: [spotBugs(pattern: '**/target/spotbugsXml.xml')])
                }
            }
        }

        stage('PMD Analysis') {
            steps {
                sh 'mvn pmd:pmd'
            }
            post {
                always {
                    recordIssues(tools: [pmdParser(pattern: '**/target/pmd.xml')])
                }
            }
        }

        stage('OWASP Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: '--format XML', odcInstallation: 'OWASP Dependency-Check'
            }
            post {
                always {
                    dependencyCheckPublisher pattern: 'dependency-check-report.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Checkmarx Scan') {
            steps {
                step([$class: 'CxScanBuilder',
                      comment: '',
                      credentialsId: '',
                      excludeFolders: '',
                      excludeOpenSourceFolders: '',
                      exclusionsSetting: 'global',
                      failBuildOnNewResults: false,
                      failBuildOnNewSeverity: 'HIGH',
                      filterPattern: '''!**/_cvs/**/*, !**/.svn/**/*,
                         !**/.hg/**/*,   !**/.git/**/*,  !**/.bzr/**/*,
                         !**/bin/**/*,
                         !**/obj/**/*,
                         !**/backup/**/*,
                         !**/.idea/**/*, !**/*.DS_Store,
                         !**/*.ipr,  !**/*.iws,
                         !**/*.bak,   !**/*.tmp,
                         !**/*.aac,   !**/*.aif,  !**/*.iff,
                         !**/*.m3u,   !**/*.mid,  !**/*.mp3,
                         !**/*.mpa,   !**/*.ra,   !**/*.wav,
                         !**/*.wma,   !**/*.3g2,  !**/*.3gp,
                         !**/*.asf,   !**/*.asx,  !**/*.avi,
                         !**/*.flv,   !**/*.mov,  !**/*.mp4,
                         !**/*.mpg,   !**/*.rm,   !**/*.swf,
                         !**/*.vob,   !**/*.wmv,  !**/*.bmp,
                         !**/*.gif,   !**/*.jpg,  !**/*.png,
                         !**/*.psd,   !**/*.tif,  !**/*.swf,
                         !**/*.jar,   !**/*.zip,
                         !**/*.rar,   !**/*.exe,  !**/*.dll,
                         !**/*.pdb,   !**/*.7z,   !**/*.gz,
                         !**/*.tar.gz,!**/*.tar,  !**/*.gz,
                         !**/*.ahtm,  !**/*.ahtml,!**/*.fhtml,
                         !**/*.hdm,   !**/*.hdml, !**/*.hsql,
                         !**/*.ht,    !**/*.hta,  !**/*.htc,
                         !**/*.htd,   !**/*.war,  !**/*.ear,
                         !**/*.htmls, !**/*.ihtml,!**/*.mht,
                         !**/*.mhtm,  !**/*.mhtml,!**/*.ssi,
                         !**/*.stm,   !**/*.stml, !**/*.ttml,
                         !**/*.txn,   !**/*.xhtm, !**/*.xhtml,
                         !**/*.class, !**/*.iml,  !Checkmarx/Reports/*.*''',
                      fullScanCycle: 10,
                      groupId: '1',
                      includeOpenSourceFolders: '',
                      osaArchiveIncludePatterns: '*.zip, *.war, *.ear, *.tgz',
                      password: '{XXXXXXXX}',
                      preset: '36',
                      projectName: 'YourProjectName',
                      sastEnabled: true,
                      serverUrl: 'http://localhost:8080',
                      sourceEncoding: '1',
                      username: '',
                      vulnerabilityThresholdResult: 'FAILURE',
                      waitForResultsEnabled: true])
            }
        }
    }

    post {
        always {
            recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
