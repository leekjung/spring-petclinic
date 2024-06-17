pipeline {
    agent any
    
    tools {
        jdk 'JDK17'
        maven 'M3'
    }
    environment {
        // jenkins에 등록해 놓은 docker hub credentials 이름
        DOCKERHUB_CREDENTIALS = credentials('Dockerhub-jenkis')
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
        stage('Docker Login') {
            steps {
                // docker hub 로그인
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --passwword-stdin'
            }
        }    
        stage ('Docker Image Push') {
            steps {
                // docker hub에 이미지 업로드
                sh 'docker push alexuna/spring-petclinic:latest'
            }
        }    
        stage ('Docker Image Remove') {
            steps {
                // docker image 삭제
                sh """
                docker rmi alexuna/spring-petclinic:$BUILD_NUMBER
                docker rmi alexuna/spring-petclinic:latest
                """
            }
        }
    }
}
