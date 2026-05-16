pipeline{
    agent {label "agent"}
    triggers {
        pollSCM("* * * * *")
    }
    parameters{
        booleanParam(name: "Skip_Build", defaultValue: true, description: "Skip build maven")
        booleanParam(name: "Skip_Sonar", defaultValue: true, description: "Skip sonar scan")
        booleanParam(name: "Skip_docker", defaultValue: true, description: "Skip docker")
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
            when {
                expression {
                    return !params.Skip_Build
                }
            }
            steps {
                sh "mvn package"
            }
        }

        stage ("Sonar-scan") {
            when {
                expression {
                    return !params.Skip_Sonar
                }
            }
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
            when {
                expression {
                    return !params.Skip_docker
                }
            }
            steps {
                sh "docker image build -t ${image_name}:${tag_name} ."
            }
        }

        
    stage ('trivy report') {
      steps {
        sh '''
          curl -sSL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/junit.tpl -o junit.tpl

          trivy image \
            --scanners vuln \
            --severity UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL \
            --format template \
            --template "@junit.tpl" \
            -o trivy-report.xml \
            ${image_name}:${tag_name}
        '''
      }
    }


        // stage ("Docker Image push to ECR") {
        //     steps {
        //         sh """
                
                
        //         """
        //     }
        // }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            archiveArtifacts artifacts: 'target/surefire-reports/*.xml'
            junit '**/target/surefire-reports/*.xml' 
        }
    }
}