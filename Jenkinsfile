pipeline{
    agent any

    tools{
        maven 'maven'
    }

    stages{
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/KwasiPee/web-app.git'
            }
        }

        stage('maven build'){
            steps{
                sh 'mvn clean package'
            }
        }

        stage('Code analysis'){
           environment {
               ScannerHome = tool 'sonar'
           }
           steps{
               script{
                   withSonarQubeEnv('sonar'){
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=sarps-webapp"
                   }
               }
           }
       }

       stage('upload to nexus'){
            steps{
                nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', classifier: '', file: '/var/lib/jenkins/workspace/jomacs-webapp-jenkinsfile/target/web-app.war', type: 'war']], credentialsId: 'nexus-credentials', groupId: 'com.mt', nexusUrl: '3.76.45.237:8081/repository/jomacs-webapp', nexusVersion: 'nexus3', protocol: 'http', repository: 'jomacs-webapp', version: '3.0.6-RELEASE'
            }
       }

       stage('prod deployment'){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'http://3.124.214.70:8080/')], contextPath: null, war: 'target/web-app.war'
            }
       }
    }

    post {
        success {
            slackSend channel: 'jenkinstest', color: 'good', message: "Build successful: ${currentBuild.fullDisplayName}"
        }
        failure {
            slackSend channel: 'jenkinstest', color: 'danger', message: "Build failed: ${currentBuild.fullDisplayName}"
        }
        aborted {
            slackSend channel: 'jenkinstest', color: 'warning', message: "Build aborted: ${currentBuild.fullDisplayName}"
        }
        }
}
