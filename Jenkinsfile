pipeline {
    agent any
    
    tools{
        maven 'MAVEN'        
    }
    environment {
        AWS_REGION = 'ap-northeast-1'  // 使用するAWSリージョン
        ECR_REPO_NAME = 'testecr12'
        AWS_ACCOUNT_ID = '590183987209'
        IMAGE_NAME = 'test-image'
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
        stage('Setup AWS Credentials') {
            steps {
                script {
                    // Jenkinsの認証情報ストアからAWS認証情報を取得
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'MyAWS']]) {
                        // AWS CLIを使用してECRのログインパスワードを環境変数に設定
                        env.AWS_ECR_PASSWORD = sh(script: "aws ecr get-login-password --region $AWS_REGION", returnStdout: true).trim()
                    }
                }
            }
        }
        stage('Build and Push Docker Image') {
            steps {
                // MavenのJibプラグインを使用してイメージをビルドし、ECRにプッシュ
                sh 'mvn compile com.google.cloud.tools:jib-maven-plugin:build -Daws.account.id=$AWS_ACCOUNT_ID -Daws.region=$AWS_REGION -Decr.repo.name=$ECR_REPO_NAME'
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
            echo 'Build failed!!?!'
        }
    }
}
