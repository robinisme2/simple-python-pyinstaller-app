pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') { 
            agent {
                docker {
                    image 'qnib/pytest' 
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py' 
            }
            post {
                always {
                    junit 'test-reports/results.xml' 
                }
            }
        }
        stage('Publish') {
            steps {
                docker.withRegistry('http://192.168.101.41:5000') {
                    def customImage = docker.build("jenkins-test-image:${env.BUILD_ID}")
                    /* Push the container to the custom Registry */
                    customImage.push()
                }
            }
        }
    }
}
