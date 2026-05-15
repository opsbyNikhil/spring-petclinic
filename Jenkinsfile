pipeline {
    agent {label "Java"}
    triggers {
        pollSCM("* * * * *")
    }
    stages {
        stage ("Git - Checkout") {
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

        stage ("Sonar") {
            steps {
                withCredentials([string(credentialsId: "SONAR", variable: "SONAR_TOKEN")]){
                withSonarQubeEnv("SONAR"){
                    sh """ mvn package sonar:sonar \
                    -Dsonar.ProjectKey=opsbyNikhil_spring-petclinic \
                    -Dsonar.organization=opsbyNikhil \
                    -Dsonar.host.url=https://sonarcloud.io/ \
                    -Dsonar.login=$SONAR_TOKEN
                    """
                }
                }
            }
        }
    }
}