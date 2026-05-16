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
    }
}