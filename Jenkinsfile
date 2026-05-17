pipeline {
    agent { label 'k8s-agent-python' }
    
    environment {
        DEERFLOW_WEBHOOK_URL = 'http://115.190.230.80:8001'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/high0528/cicd-verify-python.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                container('python') {
                    sh 'pip install --cache-dir /root/.cache/pip -r requirements.txt'
                }
            }
        }
        
        stage('Test') {
            steps {
                container('python') {
                    sh 'pytest --junitxml=test-results.xml'
                }
            }
        }
    }
    
    post {
        success {
            sh "curl -s -X POST ${DEERFLOW_WEBHOOK_URL}/webhooks/jenkins -H 'Content-Type: application/json' -d '{\"job_name\":\"${env.JOB_NAME}\",\"build_number\":${env.BUILD_NUMBER},\"status\":\"SUCCESS\",\"branch\":\"${env.BRANCH_NAME ?: 'main'}\"}' || true"
        }
        failure {
            sh "curl -s -X POST ${DEERFLOW_WEBHOOK_URL}/webhooks/jenkins -H 'Content-Type: application/json' -d '{\"job_name\":\"${env.JOB_NAME}\",\"build_number\":${env.BUILD_NUMBER},\"status\":\"FAILURE\",\"branch\":\"${env.BRANCH_NAME ?: 'main'}\"}' || true"
        }
    }
}