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
    }
}