pipeline {
    environment {
        registry = "liseon/python-jenkins"
        dockerImage = ''
    }
    agent any
    stages {
        stage('checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs:
                                [[credentialsId: 'github-creds', url: 'https://github.com/Liseon617/flask-pytest-docker-jenkins.git']])
            }
        }
        
        stage('Stop previous running container') {
            steps {
                sh returnStatus: true, script: 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
                sh returnStatus: true, script: 'docker rm ${JOB_NAME}'
            }
        }

        stage('Build Docker Image and Docker Container') {
            steps {
                script {
                    // img = registry + ":${env.BUILD_ID}"
                    // println ("${img}")
                    // dockerImage = docker.build("${img}")
                    sh "docker compose -f docker-compose.yaml up --abort-on-container-exit --exit-code-from test"
                }
            }
        }

        stage("Test") {
            steps {
                junit keepProperties: true, skipMarkingBuildUnstable: true, stdioRetention: '', testResults: 'xmlReport/output.xml'
                cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'coverage.xml', 
                conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', 
                maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
            }
        }

        stage('Deploy') {
            steps {
                echo "Deployment complete."
                // Assuming you have a script to configure Nginx and serve the application deployment
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            echo 'Pipeline completed!'
        }
    }
}
