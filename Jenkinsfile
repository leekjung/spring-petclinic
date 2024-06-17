pipeline {
    agent any
    
    tools {
        jdk 'JDK17'
        maven 'M3'
    }
    stages {
        stage('Git Clone') {
            steps {
                echo 'Git clone'
                git url: 'https://github.com/leekjung/spring-petclinic.git',
                branch: 'efficient-webjars'
            }
            post {
                success {
                    echo 'Success git clone step'
                }
                failure {
                    echo 'Fail git clone step'
                }
            }
        }
        stage('Maven build') {
            steps {
                echo 'Maven build'
                sh 'mvn -Dmaven.test.failure.ignore=true install'
            }
            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            } 
        }
        stage('Docker Image Upload') {
            steps {
                echo 'Docker Image build'
                dir("${env.WORKSPACE}") {
                    sh """
                        docker build -t alexuna/spring-petclinic:$BUILD_NUMBER .
                        docker tag alexuna/spring-petclinic:$BUILD_NUMBER alexuna/spring-petclinic:latest
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy'
            }
        }
    }
}
