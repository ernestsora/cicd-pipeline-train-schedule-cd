pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeployProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environemtn look OK?'
                milestore(1)
                withCredentials([usernamePassword(credentialsId: 'github_api', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                            publishers: [
                                sshPublisherDesc(
                                    configName: 'production',
                                    sshCredentials: [ username: '$USERNAME', encryptedPassphrase: '$USERPASS'],
                                    transfers: [
                                        sshTransfer(
                                            sourceFiles: 'dist/trainSchedule.zip',
                                            removePrefix: 'dist/',
                                            remoteDirectory: '/tmp',
                                            execCommand: 'sudo systemctl stop trainSchedule && sudo rm -rf /opt/trainSchedule/* && sudo unzip /tmp/trainSchedule.zip -d /opt/trainSchedule && sudo systemctl start trainSchedule'
                                        )
                                    ]
                                 )
                          ]
                        
                  )
               }
            }
        }
        stage('DeployStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'github_api', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                            publishers: [
                                sshPublisherDesc(
                                    configName: 'staging',
                                    sshCredentials: [ username: '$USERNAME', encryptedPassphrase: '$USERPASS'],
                                    transfers: [
                                        sshTransfer(
                                            sourceFiles: 'dist/trainSchedule.zip',
                                            removePrefix: 'dist/',
                                            remoteDirectory: '/tmp',
                                            execCommand: 'sudo systemctl stop trainSchedule && sudo rm -rf /opt/trainSchedule/* && sudo unzip /tmp/trainSchedule.zip -d /opt/trainSchedule && sudo systemctl start trainSchedule'
                                        )
                                    ]
                                 )
                          ]
                        
                  )
               }
            }
        }
    }
}
