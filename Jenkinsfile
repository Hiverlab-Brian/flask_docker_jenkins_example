pipeline {

    agent any

    environment {
        BRANCH_NAME = "${GIT_BRANCH.split("/")[1]}"
        SKIP = "FALSE"
    }

    stages {
        stage('checkout') {
            steps {
                script{
                    echo "Checking out branch: $BRANCH_NAME"
                    checkout scmGit(branches: [[name: "*/$BRANCH_NAME"]], extensions: [], userRemoteConfigs: 
                    [[credentialsId: 'hiverlab-deployment', url: 'git@github.com:HiverlabResearchAndDevelopment/IFC_Converter.git']])
                    
                    // get conventional commit message 
                    def commitMessage = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                    echo "Commit message: ${commitMessage}"

                    // check if the conventional commit message contains "refactor" or "style" 
                    def matcher  = commitMessage =~ /(?i)(refactor|style|ci)/
                    def match = matcher.find()
                    echo "commit skippable?: ${match}"
                    if (match) {
                        echo "Skipping build due to non-essential changes: ${commitMessage}"
                        SKIP="TRUE"
                        return // Exit stage gracefully
                    }
                }
            }
        }

        stage('Build') {
            when {
                expression { SKIP == "FALSE" }
            }
            steps {
                script {               
                    script {
                        def containers = sh(script: 'docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\'', returnStdout: true).trim()
                        if (containers) {
                            sh "docker stop ${containers} || true"
                            sh "docker rm ${containers} || true"
                        }
                    }

                        // Ensure docker-compose.yaml is present
                        if (!fileExists('docker-compose.yaml')) {
                            error "docker-compose.yaml not found"
                        }
                        sh "docker compose -f docker-compose.yaml up --abort-on-container-exit --exit-code-from test"
                    }
                }
            }
        }

        stage("Test") {
            when {
                expression { SKIP == "FALSE" }
            }
            steps {
                junit keepProperties: true, skipMarkingBuildUnstable: true, stdioRetention: '', testResults: 'xmlReport/output.xml'
                cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'coverage.xml', 
                conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', 
                maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
            }
        }

        stage("Deploy") {
            when {
                expression { SKIP == "FALSE" }
            }
            steps {
                script {
                    echo "Deploying to demo application to $BRANCH_NAME Environment @ vlsdemo"
                    withCredentials([
                        sshUserPrivateKey(credentialsId: 'vlsdemo-ssh-deployment-acc', keyFileVariable: 'SSH_KEY'),
                        string(credentialsId: 'brian-vlsdemo-vm-ip', variable: 'REMOTE_SERVER'),
                        ]) {
                            if(BRANCH_NAME == 'main') {
                                sshagent(['hiverlab-deployment']) {
                                    sh '''
                                        ssh deployment@$REMOTE_SERVER "
                                        cd /home/deployment/prod/flask_docker_jenkins_example && 
                                        git pull origin $BRANCH_NAME && 
                                        docker-compose down && 
                                        docker-compose up -d --build"
                                    '''
                                }
                            } else if (BRANCH_NAME == 'dev') {
                                sshagent(['hiverlab-deployment']) {
                                    sh '''
                                        ssh deployment@$REMOTE_SERVER "
                                        cd /home/deployment/dev/flask_docker_jenkins_example && 
                                        git pull origin $BRANCH_NAME && 
                                        docker-compose down && 
                                        docker-compose up -d --build"
                                    '''
                                }
                            } else {
                                echo "Invalid branch detected"
                                return
                            }
                    }
                }
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
            echo 'Pipeline completed.'
        }
    }
}
