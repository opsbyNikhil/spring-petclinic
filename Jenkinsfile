pipeline{
    agent {label "agent"}
    triggers {
        pollSCM("* * * * *")
    }

    stages {
        stage ("Git-Checkout") {
            step {
                git url: "https://github.com/opsbyNikhil/spring-petclinic.git",
                    branch: "main"
            }
        }
    }
}