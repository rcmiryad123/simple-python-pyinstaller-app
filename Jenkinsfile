node {
    try {
        stage('Build') {
            docker.image('python:3.11.5-alpine3.18').inside() {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        
        stage('Test') {
            docker.image('qnib/pytest').inside() {
                try {
                    sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
                } finally {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Deliver') {
            def volume = "$(pwd)/sources:/src"
            def image = 'cdrx/pyinstaller-linux:python2'
            
            dir(path: env.BUILD_ID) {
                unstash(name: 'compiled-results')
                sh "docker run --rm -v ${volume} ${image} 'pyinstaller -F add2vals.py'"
                
                if (currentBuild.resultIsBetterOrEqualTo('SUCCESS')) {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                    sh "docker run --rm -v ${volume} ${image} 'rm -rf build dist'"
                }
            }
        }
    } catch (Exception e) {
        currentBuild.result = 'UNSTABLE'
    }
}
