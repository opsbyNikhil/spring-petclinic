pipeline{
    agent: {label: "JAVA   "}
    triggers{
        pollSCM("* * * * *")
    }
    stages{
        stage ("Git - checkout"){
            steps{
                git url: "https://github.com/opsbyNikhil/spring-petclinic.git",
                    branch: "main"
            }
        }
    }
}