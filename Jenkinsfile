pipeline {
    agent any

    environment {
        ECR_PATH = '851557167064.dkr.ecr.ap-northeast-2.amazonaws.com'
        ECR_IMAGE = 'bastion'
        REGION = 'ap-northeast-2'
        ACCOUNT_ID='851557167064'
    }

    stages {
        stage('Git Clone from gitSCM') {
            steps {
                script {
                    try {
                        git branch: 'main',
                            credentialsId: 'git',
                            url: 'https://github.com/imyujinsim/project'
                        sh "ls -lat"
                        pwd
                        env.cloneResult=true
                    } catch (error) {
                        print(error)
                    }
                }
            }
        }
        stage('Docker Build'){
            steps {
                script{
                    try {
                        sh """
                        #!/bin/bash
                        cat>Dockerfile<<-EOF
FROM imyujinsim/tomcat8888:v1
ADD server.xml /usr/local/tomcat/conf/server.xml
ADD redis-data-cache.properties /usr/local/tomcat/conf/redis-data-cache.properties
ADD index.jsp /usr/local/tomcat/webapps/ROOT/index.jsp
WORKDIR /usr/local/tomcat/bin
CMD ["./catalina.sh", "run"]
EOF
"""
                } catch (error) {
                        print(error)
                    }
            }
        }
        }
        stage('Push to ECR'){
            steps {
              script {
                docker.withRegistry("https://${ECR_PATH}", "ecr:ap-northeast-2:aws_credentials") {
                    def image = docker.build("${ECR_PATH}/${ECR_IMAGE}:${env.BUILD_NUMBER}")
                        image.push()
                }

                echo 'Remove Deploy Files'
                sh "sudo rm -rf /var/lib/jenkins/workspace/${env.JOB_NAME}/*"
                env.dockerBuildResult=true
              }
            }
        }
    }
}
