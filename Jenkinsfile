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
        // stage('Checkstyle Analysis') {
        //     steps {
        //         // Mavenを使用してCheckstyleを実行する
        //         sh 'mvn checkstyle:checkstyle'
        //     }
        // }
        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('SonarQube_server'){
        //             sh "mvn sonar:sonar"
        //         }
        //     }
        // }
        stage('Test') {
            steps {
				sh 'mvn test'
                junit 'target/surefire-reports/*.xml'
            }
        }

    }
    post {
        always {
            // Checkstyleレポートを生成し、Jenkinsに結果を提供する
            junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
            // PMDレポートをJenkinsに公開
            // pmd canComputeNew: true, pattern: '**/target/pmd/pmd.xml'
            // // CheckstyleレポートをJenkinsに公開
            // checkstyle canComputeNew: true, pattern: '**/target/checkstyle-result.xml'
        }
        success {
            // ビルドが成功した場合に実行するステップを記述する
            echo 'Build succeeded!'
        }
        failure {
            // ビルドが失敗した場合に実行するステップを記述する
            echo 'Build failed!!?'
        }
    }
}
