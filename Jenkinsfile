pipeline {
  agent any
  environment {
    AUTH = credential("vpn_credential")
    VPN_CON = credential("openvpn_con")
    HOST = '10.182.233.21'
    USER = 'amalitech'
  }
  stages {
    stage("Connect to remote server") {
      steps {
        step {
          script {
            sh """
              echo $AUTH > vpn_credential.txt
              echo $VPN_CON > GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn 
            """
          }
        }
        step{
          script {
            sh "sudo openvpn --config GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn --auth-user-pass vpn_credential.txt --auth-nocache &"
            sleep(time: 10, unit: 'SECONDS')
          }
        }
      }
    }
    stage("Move application") {
      steps {
        script {
          sh "scp -i ~/.ssh/id_rsa_a . $USER@$HOST:/home/projects/"
        }
      }
    }
    stage("Start Application") {
      steps {
        script { 
          sh "ssh -i ~/.ssh/id_rsa_a $USER@$HOST 'cd /home/projects/icon-test && docker compose up'"
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

