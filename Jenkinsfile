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
                withCredentials(string[crdentialsId: "SONAR_id", variable: "SONAR_TOKEN"]){
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
    }
}