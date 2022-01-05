pipeline {
    agent  {
     label 'docker'
           }
    stages {
        stage("Run molecule"){
            steps {
              git branch: 'Branch-9.5', url: 'https://github.com/olegrovenskiy/HW-9.5.git'
              echo 'Hello'
              sh '''pip3 install "molecule==3.4.0"
              pip3 install molecule-docker
              cd ./roles/kibana-role
              molecule test'''
            }
        }
    }
}
