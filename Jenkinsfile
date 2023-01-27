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
        stage('Stage'){
            when {
                branch 'master'
            }
            steps {
              withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]){
                  sshPublisher(
                      continueOnError: false,
                      failOnError: true,
                      publishers: [
                        sshPublisherDesc(
                          configName: 'staging',
                            sshCredentials: [
                              username: "$USERNAME",
                              encryptedPassphrase: "$USERPASS"
                           ],
                            transfers: [
                              sshTransfer (
                                sourceFiles: 'dist/trainSchedule.zip',
                                removePrefix: 'dist',
                                remoteDirectory: '/tmp',
                                execCommand: 'sudo /usr/bin/systemctl stop train-schedule && sudo rm -rf /opt/train-schedule && sudo unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
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
