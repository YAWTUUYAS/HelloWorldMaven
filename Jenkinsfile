pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
        jdk 'jdk-21'
    }

    environment {
        // Le token Sonar stocké dans Jenkins
        SONAR_AUTH_TOKEN = credentials('SONAR_AUTH_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/YAWTUUYAS/HelloWorldMaven.git', credentialsId: 'new'
            }
        }

        stage('Build') {
            steps {
                withMaven(maven: 'apache-maven-3.9.6') {
                    sh "mvn clean compile"
                }
            }
        }

        stage('Test') {
            steps {
                withMaven(maven: 'apache-maven-3.9.6') {
                    sh "mvn test"
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar.tools.devops.****') {
                    withMaven(maven: 'apache-maven-3.9.6') {
                        sh """
                            mvn sonar:sonar \
                            -Dsonar.projectKey=myProject \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.login=$SONAR_AUTH_TOKEN
                        """
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy') {
            steps {
                withMaven(maven: 'apache-maven-3.9.6') {
                    sh "mvn deploy"
                }
            }
        }

        stage('Create Git Tag') {
            steps {
                script {
                    TAG = "v1.${env.BUILD_NUMBER}"
                    sh """
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"
                        git tag ${TAG}
                    """
                }
            }
        }

        stage('Push Git Tag') {
            steps {
                sh """
                    git push origin ${TAG}
                """
            }
        }
    }

    post {
        failure {
            echo "PIPELINE FAILED ❌"
        }
        success {
            echo "PIPELINE SUCCESS ✔"
        }
    }
}
