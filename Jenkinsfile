pipeline {
    agent {label "Java"}
    triggers {
        pollSCM("* * * * *")
    }

    parameters {
        booleanParam (name: "SKIP_BUILD", defaultValue: false, description: "Skip maven build stage")
    }

    stages {
        stage ("Git - Checkout") {
            steps {
                git url: "https://github.com/opsbyNikhil/spring-petclinic.git",
                    branch: "main"
            }
        }

        stage ("Build") {

            // it only reduces the execution time
            // when {
            //     expression {
            //         currentBuild.currentResult == null || currentBuild.currentResult == "SUCCESS"
            //     }
            // }

            when {
                expression {
                    return !params.SKIP_BUILD
                }
            }

            steps {
                sh "mvn package"
            }
        }

        stage ("Sonar") {
            steps {
                withCredentials([string(credentialsId: "sonar_id", variable: "SONAR_TOKEN")]){
                withSonarQubeEnv("SONAR"){
                    sh """mvn sonar:sonar \
                    -Dsonar.projectKey=opsbyNikhil_spring-petclinic \
                    -Dsonar.organization=opsbynikhil \
                    -Dsonar.host.url=https://sonarcloud.io/ \
                    -Dsonar.login=$SONAR_TOKEN
                    """
                }
                }
            }
        }

        stage ("Upload JAR in S3 Bucket") {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'jenkins-jar-file'
                ]]) {
                    sh """
                    aws s3 cp target/*.jar \
                    s3://jenkins-jar-upload-01/build-${BUILD_NUMBER}.jar
                    """
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