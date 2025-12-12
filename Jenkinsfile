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

        /* -------------------------
         * CLEAN WORKSPACE
         * ------------------------- */
        stage("Clean Workspace") {
            steps {
                cleanWs()
            }
        }

        /* -------------------------
         * GIT CHECKOUT
         * ------------------------- */
        stage("Git Checkout") {
            steps {
                git branch: 'main', url: 'https://github.com/SandraIriaka/Netflix-App.git'
            }
        }

        /* -------------------------
         * SONARQUBE SCAN - FRONTEND
         * ------------------------- */
        stage("SonarQube Scan - Frontend") {
            steps {
                dir('frontend') {    // change 'frontend' to your actual folder name
                    withSonarQubeEnv('sonar-scanner') {
                        sh """
                            ${SCANNER_HOME}/bin/sonar-scanner \
                                -Dsonar.projectKey=frontend-amazon \
                                -Dsonar.projectName=frontend-amazon \
                                -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        /* -------------------------
         * SONARQUBE SCAN - BACKEND
         * ------------------------- */
        stage("SonarQube Scan - Backend") {
            steps {
                dir('backend') {    // change 'backend' to your actual folder name
                    withSonarQubeEnv('sonar-scanner') {
                        sh """
                            ${SCANNER_HOME}/bin/sonar-scanner \
                                -Dsonar.projectKey=backend-amazon \
                                -Dsonar.projectName=backend-amazon \
                                -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        /* -------------------------
         * QUALITY GATE WAIT
         * ------------------------- */
        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 3, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: false
                    }
                }
            }
        }

        /* -------------------------
         * NPM INSTALL FRONTEND
         * ------------------------- */
        stage("Install Frontend Dependencies") {
            steps {
                dir('frontend') {
                    sh "npm install"
                }
            }
        }

        /* -------------------------
         * NPM INSTALL BACKEND
         * ------------------------- */
        stage("Install Backend Dependencies") {
            steps {
                dir('backend') {
                    sh "npm install"
                }
            }
        }

        /* -------------------------
         * OWASP Dependency Check - FRONTEND
         * ------------------------- */
        stage("OWASP Scan - Frontend") {
            steps {
                dir('frontend') {
                    dependencyCheck additionalArguments: '''
                        --scan ./
                        --disableYarnAudit
                        --disableNodeAudit
                    ''', odcInstallation: 'dp-check'

                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        /* -------------------------
         * OWASP Dependency Check - BACKEND
         * ------------------------- */
        stage("OWASP Scan - Backend") {
            steps {
                dir('backend') {
                    dependencyCheck additionalArguments: '''
                        --scan ./
                        --disableYarnAudit
                        --disableNodeAudit
                    ''', odcInstallation: 'dp-check'

                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        /* -------------------------
         * TRIVY FS SCAN - FRONTEND
         * ------------------------- */
        stage("Trivy Scan - Frontend") {
            steps {
                dir('frontend') {
                    sh "trivy fs . > trivyfs-frontend.txt"
                }
            }
        }

        /* -------------------------
         * TRIVY FS SCAN - BACKEND
         * ------------------------- */
        stage("Trivy Scan - Backend") {
            steps {
                dir('backend') {
                    sh "trivy fs . > trivyfs-backend.txt"
                }
            }
        }

    } // end stages
}
