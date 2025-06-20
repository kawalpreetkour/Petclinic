pipeline {
    agent any 
    
    tools {
        jdk 'jdk'
        maven 'Maven'
    }

    environment {
        SCANNER_HOME = tool 'Sonar-Scanner'
    }

    stages {
        stage("Git Checkout") {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/kawalpreetkour/Petclinic.git'
            }
        }

        stage("Compile") {
            steps {
                sh "mvn clean compile"
            }
        }

        stage("Test Cases") {
            steps {
                sh "mvn test"
            }
        }

        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh ''' 
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic \
                    -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                    '''
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format HTML,XML', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage("Build") {
            steps {
                sh "mvn clean install"
            }
        }
    }
}
