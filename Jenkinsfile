pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
        jdk 'jdk-21'
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/YAWTUUYAS/HelloWorldMaven.git', credentialsId: 'new'
            }
        }

        stage('Build') {
            steps {
                withMaven(maven: 'Maven-3.9.6') {
                    sh "mvn clean compile"
                }
            }
        }

        stage('Test') {
            steps {
                withMaven(maven: 'Maven-3.9.6') {
                    sh "mvn test"
                }
            }
        }

        stage('Package') {
            steps {
                withMaven(maven: 'Maven-3.9.6') {
                    sh "mvn package"
                }
            }
        }

        stage('Create Git Tag') {
            steps {
                script {
                    TAG = "v1.${env.BUILD_NUMBER}"
                    sh """
                        git config user.email "yassinebenaba17@gmail.com"
                        git config user.name "YAWTUUYAS"
                        git tag ${TAG}
                    """
                }
            }
        }

        stage('Push Git Tag') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'new', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh """
                        git push https://${GIT_USER}:${GIT_PASS}@github.com/YAWTUUYAS/HelloWorldMaven.git ${TAG}
                    """
                }
            }
        }

    }

    post {
        success {
            echo "✔ PIPELINE SUCCESS"
        }
        failure {
            echo "❌ PIPELINE FAILED"
        }
    }
}
