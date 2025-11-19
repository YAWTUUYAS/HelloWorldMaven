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

        stage('Deploy') {
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
