pipeline {
    agent any

    tools {
        nodejs 'node18'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'bms-update', url: 'https://github.com/RithikaMurugannn/Book-My-Show.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('bookmyshow-app') {
                    sh 'npm install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('bookmyshow-app') {
                    withSonarQubeEnv('sonar-server') {
                        script {
                          sh """
                          ${tool 'sonar-scanner'}/bin/sonar-scanner \
                          -Dsonar.projectKey=BMS \
                          -Dsonar.sources=.
                          """
                        }
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('bookmyshow-app') {
                    sh 'docker build -t bms:latest .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 3000:3000 bms'
            }
        }

       

    }

    post {

        success {
            emailext(
                subject: "Jenkins Build SUCCESS",
                body: """
Pipeline executed successfully.

Project: BMS
Build Number: ${env.BUILD_NUMBER}
Job: ${env.JOB_NAME}

Application deployed successfully.
""",
                to: "rithikamurugan181@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "Jenkins Build FAILED",
                body: """
Pipeline execution FAILED.

Project: BMS
Build Number: ${env.BUILD_NUMBER}
Job: ${env.JOB_NAME}

Check Jenkins console logs.
""",
                to: "rithikamurugan181@gmail.com"
            )
        }

        always {
            echo "Pipeline finished."
        }
    }
}
