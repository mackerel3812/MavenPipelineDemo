pipeline {
    agent any
    
    tools{
        maven 'MAVEN'        
    }

    stages {
        stage('Build') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'MyGitHub', url: 'https://github.com/mackerel3812/MavenPipelineDemo.git']])
            	sh "mvn clean package"
            }
        }
        stage('Test') {
            steps {
				sh 'mvn test'
                junit 'target/surefire-reports/*.xml'
            }
        }

    }
    post {
        always {
            junit(
                allowEmptyResults:true,
                testResults: '*test-reports/.xml'
                )
        }
    }
}
