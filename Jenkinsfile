node {
    stage('Build') { 
        steps {
            sh 'npm running.. "npm install"'
        }
    }
    stage('Test') {
        steps {
            sh './jenkins/scripts/test.sh'
        }
    }
}
