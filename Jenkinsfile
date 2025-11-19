pipeline {
    agent any

    environment {
        // Nom du Maven Tools configuré dans Manage Jenkins > Tools
        MAVEN_HOME = tool 'Maven-3.9.6'

        // Credentials Jenkins pour GitHub (ton token)
        GIT_CREDS = 'new'

        // URL de ton repo GitHub
        REPO_URL = 'https://github.com/YAWTUUYAS/HelloWorldMaven.git'
    }

    triggers {
        // Vérifier le dépôt toutes les minutes
        pollSCM('* * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: "${REPO_URL}",
                    credentialsId: "${GIT_CREDS}"
            }
        }

        stage('Build') {
            steps {
                withMaven(maven: 'Maven-3.9.6') {
                    sh "${MAVEN_HOME}/bin/mvn clean compile"
                }
            }
        }

        stage('Test') {
            steps {
                withMaven(maven: 'Maven-3.9.6') {
                    sh "${MAVEN_HOME}/bin/mvn test"
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar.tools.devops.****') {
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=myProject \
                        -Dsonar.sources=./src
                    """
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy') {
            steps {
                withMaven(maven: 'Maven-3.9.6') {
                    sh "${MAVEN_HOME}/bin/mvn deploy"
                }
            }
        }

        stage('Create Git Tag') {
            steps {
                script {
                    def tagName = "build-${env.BUILD_NUMBER}"

                    sh """
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"

                        git tag -a ${tagName} -m "Build ${env.BUILD_NUMBER}"
                    """
                }
            }
        }

        stage('Push Git Tag') {
            steps {
                script {
                    def tagName = "build-${env.BUILD_NUMBER}"

                    withCredentials([usernamePassword(credentialsId: "${GIT_CREDS}", usernameVariable: 'USER', passwordVariable: 'TOKEN')]) {

                        sh """
                            git push https://${USER}:${TOKEN}@github.com/YAWTUUYAS/HelloWorldMaven.git ${tagName}
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline finished successfully ✔"
        }
        failure {
            echo "PIPELINE FAILED ❌"
        }
    }
}
