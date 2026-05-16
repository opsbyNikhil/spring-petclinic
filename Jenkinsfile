pipeline{
    agent {label "agent"}
    triggers {
        pollSCM("* * * * *")
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
                withCredentails([[
                    $class: "AmazonWebServicesCredentialsBinding",
                    credentailsId: "Jenkins-JAR"
                ]]) {
                    sh """aws s3 cp target/*.jar \
                    s3://jenkins-myjar/build-${BUILD_NUMBER}.jar

                    """ 
                }
            }
        }
    }
}