pipeline {
    agent any

    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage("Clean Workspace") {
            steps { cleanWs() }
        }

        stage("Git Checkout") {
            steps { git branch: 'main', url: 'https://github.com/SandraIriaka/Netflix-App.git' }
        }

        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=amazon \
                        -Dsonar.projectKey=amazon'''
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 3, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                    }
                }
            }
        }

        stage("Install NPM Dependencies") {
            steps { sh "npm install" }
        }

        stage("OWASP FS Scan") {
            steps {
                dependencyCheck additionalArguments: '''
                    --scan ./ 
                    --disableYarnAudit 
                    --disableNodeAudit 
                ''', odcInstallation: 'dp-check'

                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage("Trivy File Scan") {
            steps { sh "trivy fs . > trivyfs.txt" }
        }

    } // end stages

} // end pipeline
