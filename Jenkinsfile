pipeline {
  agent {
    label "jenkins-node-python-agent"
  }
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
          withCredentials([file(credentialsId:'vpn_p12', variable: 'VPN_P12')]){
            sh 'cp $VPN_P12 ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.p12'
          }
          withCredentials([file(credentialsId:'vpn_tls_key', variable: 'VPN_TLS_KEY')]){
            sh 'cp $VPN_TLS_KEY ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection-tls.key'
          }

          sh "apt-get install openvpn"

          sh "cat ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn"
          sh "cat ./vpn_credential.txt"
          sh 'openvpn --config ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn --auth-user-pass ./vpn_credential.txt --auth-nocache &'
        }
      }
    }
    stage("Move application") {
      steps {
        script {
          withCredentials([file(credentialsId: 'private_key', variable: 'RSA')]){
            sh 'cp $RSA ./id_rsa_a'
          }
          sh 'chmod 600 ./id_rsa_a'
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
      sh 'rm -f ./vpn_credential.txt ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.ovpn ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection.p12 ./GW4_ICON_AMALITECHVPN_SERVER_NathanVPNConnection-tls.key ./id_rsa_a'
      cleanWs()
      sh "pkill openvpn || true"
    }
    success {
      echo "Successfully deployed!!!!"
    }
    failure {
      echo "Deployment failed!!!"
    }
  }
}

