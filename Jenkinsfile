pipeline {
  agent any
  environment {
    HOST = '10.182.233.21'
    USER = 'amalitech'
  }
  stages {
    stage("Connect to remote server") {
      steps {
        script {
          withCredentials([file(credentialsId:'vpn_credential', variable: 'VPN_CREDENTIAL')]){
            sh 'cp $VPN_CREDENTIAL ./vpn_credential.txt'
          }
          withCredentials([file(credentialsId:'vpn_config', variable: 'VPN_CONFIG')]){
            sh 'cp $VPN_CONFIG ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn'
          }
   
          sh 'sudo openvpn --config ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn --auth-user-pass ./vpn_credential.txt --auth-nocache &'
          sleep(time: 10, unit: 'SECONDS')
        }
      }
    }
    stage("Move application") {
      steps {
        script {
          withCredentials([file(credentialsId: 'private_key', variable: 'RSA')]){
            sh 'cp $RSA ./id_rsa_a'
          }
          sh 'scp -i ./id_rsa_a . $USER@$HOST:/home/projects/'
        }
      }
    }
    stage("Start Application") {
      steps {
        script { 
          sh "ssh -i ./id_rsa_a $USER@$HOST 'cd /home/projects/icon-test && docker compose up'"
        }
      }
    }
  }
  post {
    always {
      cleanWs()
    }
    success {
      echo "Successfully deployed!!!!"
    }
    failure {
      echo "Deployment failed!!!"
    }
  }
}

