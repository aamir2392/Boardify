pipeline {
    agent any

    tools {
        nodejs 'NodeJS' // Replace with the name of your NodeJS installation
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    sh 'npm install --verbose'
                    sh 'npm run build'
                }
            }
        }

        stage('Build Backend') {
            steps {
                script {
                    // Ensure Composer is in the PATH
                    env.PATH = "/usr/local/bin:$PATH"
                    sh 'composer install'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def remoteDirectory = "/var/www/html"

                    // Clean the remote directory before deploying
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@16.170.210.115 'rm -rf ${remoteDirectory}/*'"

                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'ApacheServer', // Replace with your actual SSH server configuration name
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: '*/',
                                        remoteDirectory: remoteDirectory,
                                        removePrefix: '',
                                        execCommand: ''
                                    )
                                ],
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false,
                                verbose: true
                            )
                        ]
                    )
                }
            }
        }

        stage('Start Vue Frontend') {
            steps {
                script {
                    sh 'npm run dev'
                }
            }
        }

        stage('Start Laravel Server') {
            steps {
                script {
                    def remoteDirectory = "/var/www/html"

                    // Start the Laravel server on the remote server
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@16.170.210.115 'cd ${remoteDirectory} && nohup php artisan serve --host=0.0.0.0 --port=8000 > /dev/null 2>&1 &'"
                }
            }
        }
    }
}
