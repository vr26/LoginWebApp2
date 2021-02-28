pipeline {
agent any

stages {
stage('Fetch Code') {
steps {
git branch: 'master', url: 'https://github.com/vr26/LoginWebApp2'
}
}
stage('BUILD') {
steps {
sh 'mvn clean install'
}
}
stage('Code Analysis With SonarQube') {
environment{
scannerHome = tool 'SonarQube-4.4.0.2'
}
steps {
withSonarQubeEnv('Sonar-Profile'){
sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
-Dsonar.projectName=vprofile-repo \
-Dsonar.projectVersion=1.0 \
-Dsonar.sources=src/ \
-Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
-Dsonar.junit.reportsPath=target/surefire-reports/ \
-Dsonar.jacoco.reportsPath=target/jacoco.exec \
-Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
}
timeout(time: 10, unit: 'MINUTES') {
waitForQualityGate abortPipeline: true
}
}
}
stage('Ansible Init') {
steps {
script {
def tfHome = tool name: 'Ansible'
env.PATH = "${tfHome}:${env.PATH}"
sh 'ansible --version'
}
}
}
stage('Ansible Deploy') {
steps {
sh "ansible-playbook /opt/tomcat.yml --user jenkins --key-file ~/.ssh/id_rsa"
}
}
}
}