pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
    }
    stages {
        stage('Checkout from Git') {
            steps{
                git branch: 'main', url: 'https://github.com/Saiprakash02/hotstar-clone-jenkins.git'
            }
        }

        stage("Sonarqube Analysis ") {
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Hotstar \
                    -Dsonar.projectKey=Hotstar'''
                }
            }
        }

        stage("quality gate") {
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                script {
                    timeout(time: 60, unit: 'MINUTES') {
                        dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                        dependencyCheckPublisher pattern: 'dependency-check-report.xml'
                        dependencyCheckPublisher pattern: 'dependency-check-report.xml', failedNewCritical: 0, failedNewHigh: 0, failedTotalCritical: 0, failedTotalHigh: 0, pattern: '', stopBuild: true, unstableNewCritical: 0, unstableNewHigh: 0, unstableTotalCritical: 0, unstableTotalHigh: 0
                    }
                }
            }
        }
    }
}
