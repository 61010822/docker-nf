pipeline {
    agent {label 'slave01'}

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'application_security', description: 'Name for the Docker image')
    }
    environment {
        TAG_NAME = "$BUILD_NUMBER"
        prisma_console_url = "https://asia-southeast1.cloud.twistlock.com/aws-singapore-961149791"
        prisma_access_id = "a826c107-8a9d-4dcf-870e-aeec33ca77a7"
        prisma_access_secret = "wFKLd3VzpRmSoJ7ZLU5b419iJTs="
    }

    stages {
        stage('Build Docker image') {
            steps {
                script {
                    sh """
                        docker build --no-cache -t $params.IMAGE_NAME:$env.TAG_NAME .
                    """
                        env.scanned_image = "$params.IMAGE_NAME:$env.TAG_NAME"
                }
            }
        }

        stage('Scan image with Prisma') {
            steps {
                script {
                    def command = """
                    /apps/devsecops/prisma/twistcli images scan --address $env.prisma_console_url \
                    -u $env.prisma_access_id \
                    -p $env.prisma_access_secret \
                    --details $env.scanned_image
                    """ 

                    sh command
                }
            }
        }
  

        // stage('Push image to Harbor') {
        //     steps {
        //         script {
        //             sh """
        //             docker push $env.scanned_image
        //             """

        //             }
        //         }
        //     }
        // }
    

    post {
        always {
            deleteDir()
            sh "docker rmi $env.scanned_image"
        }
    }
}