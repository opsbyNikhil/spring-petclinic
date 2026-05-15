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
                withCredentials([string(credentialsId: "sonar_id", variable: "SONAR_TOKEN")]){
                withSonarQubeEnv("SONAR"){
                    sh """mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=opsbyNikhil_spring-petclinic \
                    -Dsonar.organization=opsbynikhil \
                    -Dsonar.host.url=https://sonarcloud.io/ \
                    -Dsonar.login=$SONAR_TOKEN
                    """
                }
                }
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