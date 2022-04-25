pipeline {
    agent any

    tools {
        maven '3.8.5'
    }

    environment {
        APP_VERSION = sh(
                    script: '''
                            perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml
                        ''',
                        returnStdout: true
                    ).trim()
         AWS_DEFAULT_REGION="ap-south-1"
  }
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'improved', changelog: false, credentialsId: 'github', poll: false, url: 'git@github.com:jeri7758/spring-boot-jsp.git'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publishing Artifcats') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws_cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                s3Upload(file:"target/news-${APP_VERSION.trim()}.jar", bucket:'jeribucket123', path:'spring-news-app/')
                }
            }
        }
    }
}
