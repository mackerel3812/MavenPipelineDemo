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
        stage('PMD Analysis') {
            steps {
                // Mavenを使用してPMDを実行する
                sh 'mvn pmd:pmd'
            }
        }
        stage('Checkstyle Analysis') {
            steps {
                // Mavenを使用してCheckstyleを実行する
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube_server'){
                    sh "mvn sonar:sonar"
                }
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
