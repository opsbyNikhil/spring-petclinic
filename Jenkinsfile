pipeline{
    agent {label "agent"}
    triggers {
        pollSCM("* * * * *")
    }

    environment {
        image_name = "spc-1.0"
        tag_name = "1.0"
    }

    stages {
        stage ("Git-Checkout") {
            steps {
                git url: "https://github.com/opsbyNikhil/spring-petclinic.git",
                    branch: "main"
            }
        }

        stage ("Build") {
            steps {
                sh "mvn package"
            }
        }

        stage ("Sonar-scan") {
            steps {
                withCredentials([string(credentialsId: "SONAR_ID", variable: "SONAR_TOKEN")]){
                withSonarQubeEnv("SONAR"){
                    sh """
                        mvn package sonar:sonar \
                        -Dsonar.projectKey=opsbyNikhil_spring-petclinic \
                        -Dsonar.organization=opsbynikhil \
                        -Dsonar.host.url=https://sonarcloud.io/ \
                        -Dsonar.login=${SONAR_TOKEN}
                    """
                }
                }
            }
        }

        stage ("Upload JAR in S3") {
            steps {
                withCredentials([[
                    $class: "AmazonWebServicesCredentialsBinding",
                    credentialsId: "Jenkins-JAR"
                ]]) {
                    sh """aws s3 cp target/*.jar \
                    s3://jenkins-myjar/build-${BUILD_NUMBER}.jar

                    """ 
                }
            }
        }

        stage ("Docker Image") {

            steps {
                sh "docker image build -t ${image_name}:${tag_name} ."
            }
        }

        stage ("Docker Image push to ECR") {
            steps {
                sh """
                    aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 984912521466.dkr.ecr.ap-south-1.amazonaws.com 
                    docker tag ${image_name}:${tag_name} 984912521466.dkr.ecr.ap-south-1.amazonaws.com/spc/image:latest 
                    docker push 984912521466.dkr.ecr.ap-south-1.amazonaws.com/spc/image:latest
                """
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            archiveArtifacts artifacts: 'target/surefire-reports/*.xml'
            junit '**/target/surefire-reports/*.xml' 
        }
    }
}
