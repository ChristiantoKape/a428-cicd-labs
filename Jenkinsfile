node {
    stages('Build') {
        sh 'npm install'
    }
    stage('Test') { 
        sh './jenkins/scripts/test.sh' 
    }
}