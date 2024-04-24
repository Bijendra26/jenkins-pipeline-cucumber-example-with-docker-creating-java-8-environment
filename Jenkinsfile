pipeline {
    agent {
        docker {
            image 'maven:3.6.3-jdk-8'
            args '-v /tmp:/var/lib/jenkins'
        }
    }

    stages {
        stage('Checkout SCM') {
            steps {
                script {
                    checkoutSCM()
                }
            }
        }

        stage('Compiling and Running Test Cases') {
            steps {
                script {
                    compileAndRunTests()
                }
            }
        }

        stage('Generating a Cucumber Reports') {
            steps {
                script {
                    generateCucumberReports()
                }
            }
        }

        stage('Creating Package') {
            steps {
                script {
                    createPackage()
                }
            }
        }

        stage('Adding Generate Report') {
            steps {
                script {
                    addGenerateReport()
                }
            }
        }

        stage('Install SonarQube CLI') {
            steps {
                script {
                    installSonarQubeCLI()
                }
            }
        }

        stage('Analyzing Code Quality') {
            steps {
                script {
                    analyzeCodeQuality()
                }
            }
        }
    }
}

def checkoutSCM() {
    checkout scm: [$class: 'GitSCM', branches: [[name: '*/feature-test-pipeline-cucumber']], userRemoteConfigs: [[url: 'https://github.com/Bijendra26/jenkins-pipeline-cucumber-example-with-docker-creating-java-8-environment.git']]]
}

def compileAndRunTests() {
    sh 'mvn clean'
    sh 'mvn compile'
    sh 'mvn test'
}

def generateCucumberReports() {
    cucumber buildStatus: "UNSTABLE",
    fileIncludePattern: "**/cucumber.json",
    jsonReportDirectory: 'target'
}

def createPackage() {
    sh 'mvn package'
}

def addGenerateReport() {
    sh 'mvn verify'
}

def installSonarQubeCLI() {
    sh '''
        wget -O sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
        sudo unzip -o -q sonar-scanner.zip
        sudo rm -rf /opt/sonar-scanner
        sudo mv --force sonar-scanner-5.0.1.3006-linux /opt/sonar-scanner
        sudo sh -c 'echo "#/bin/bash \nexport PATH=\\\"$PATH:/opt/sonar-scanner/bin\\\"" >/etc/profile.d/sonar-scanner.sh'
        sudo chmod +x /opt/sonar-scanner/bin/sonar-scanner
        . /etc/profile.d/sonar-scanner.sh
    '''
}

def analyzeCodeQuality() {
    sh '''
        /opt/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=Bijendra26_javaprojSonarQube \
        -Dsonar.organization=bijendra26 \
        -Dsonar.qualitygate.wait=true \
        -Dsonar.qualitygate.timeout=300 \
        -Dsonar.sources=src/main/java/ \
        -Dsonar.java.binaries=target/classes \
        -Dsonar.host.url=https://sonarcloud.io \
        -Dsonar.login=346ff6dbed0434e40cc995e3a1884b84bf652c52
    '''
}
