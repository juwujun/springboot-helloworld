pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                withMaven(maven:'maven3'){
                            sh 'mvn -B -DskipTests clean package'
                    }
            }
        }
        stage('Test') {
            steps {
                withMaven(maven:'maven3'){
                sh 'mvn test -Dversion=${BUILD_NUMBER}'
                archiveArtifacts 'target/surefire-reports/*.xml'
                }
            }
            post {
                always {
                    tapdTestReport frameType: 'JUnit', onlyNewModified: true, reportPath: 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('analysis') {
            steps {
                withSonarQubeEnv('DevOpsSonarQube') {
                    withMaven(maven:'maven3'){
                        sh 'mvn -X sonar:sonar'
                    }
                }
            }
        }
        
        stage('Package'){
            steps{
                nexusPublisher nexusInstanceId: 'DevOpsNexus', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/my-app-2.0.jar']], mavenCoordinate: [artifactId: 'my-app', groupId: 'william', packaging: 'jar', version: '${BUILD_NUMBER}']]]
            }
        }
        stage('Deliver') {
            steps {
                sh 'sh ./deliver.sh'
            }
        }
    }
}
