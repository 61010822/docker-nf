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
        stage('Extract Repository Name') {
            steps {
                script {
                    def repoName = gitUrl.tokenize('/')[3..4].join('/').replace('.git', '')
                    echo "Repository Name: ${repoName}"
                }
            }
        }
        // stage('Authentication') {
        //     steps {
        //         script {
        //             authenticate = {
        //                 withCredentials([usernamePassword(credentialsId: 'prisma-pink', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
        //                     def response = sh(
        //                         script: """curl -s -w '%{http_code}' -L -X POST '${BASE_URL}login' \
        //                             -H 'Content-Type: application/json' \
        //                             -H 'Accept: application/json' \
        //                             --data-raw '{
        //                             "username": "${TL_USER}", 
        //                             "password": "${TL_PASS}"
        //                             }'""",
        //                         returnStdout: true
        //                     ).trim()

        //                     def statusCode = response.substring(response.length() - 3) 
        //                     def responseBody = response.substring(0, response.length() - 3)

        //                     if (statusCode == '200') {
        //                         echo "Authentication successfully"
        //                         def jsonResponse = readJSON text: responseBody
        //                         def token = jsonResponse.token
        //                         env.TOKEN = token
        //                     } else {
        //                         error "Failed to Authenticatio to Prisma Cloud. status: ${statusCode}"
        //                     }
        
                            
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage('Add Repositories') {
        //     steps {
        //         script {
        //             authenticate()
        //             def token = env.TOKEN
                    
        //             if (!token) {
        //                 error 'No authentication token available.'
        //             }
                    
        //             def response = sh(
        //                 script: """curl -w '%{http_code}' -L -X POST '${BASE_URL}code/api/v1/repositories' \
        //                     -H 'Content-Type: application/json' \
        //                     -H 'Accept: application/json' \
        //                     -H 'Authorization: Bearer $token' \
        //                     --data-raw '{ 
        //                     "type": "github",
        //                     "data": ["Pittimon/docker-nf"]
        //                     }'""",
        //                 returnStdout: true
        //             ).trim()

        //             def statusCode = response.substring(response.length() - 3) 
        //             def responseBody = response.substring(0, response.length() - 3)


        //             if (statusCode == '200') {
        //                 echo "Successfully Add Repository"

        //             } else {
        //                 error "Failed to Add Repository to Prisma Cloud. status: ${statusCode}"
        //             }
                
        //         }
        //     }
        // }

        // stage('Build Docker image') {
        //     steps {
        //         script {
        //             sh """
        //                 docker build --no-cache -t $params.IMAGE_NAME:$env.TAG_NAME .
        //             """
        //                 env.scanned_image = "$params.IMAGE_NAME:$env.TAG_NAME"
        //         }
        //     }
        // }

        // stage('Scan image with Prisma') {
        //     steps {
        //         script {
        //             def command = """
        //             /apps/devsecops/prisma/twistcli images scan --address $env.prisma_console_url \
        //             -u $env.prisma_access_id \
        //             -p $env.prisma_access_secret \
        //             --details $env.scanned_image
        //             """ 

        //             sh command
        //         }
        //     }
        // }
  

        // stage('Push image to Harbor') {
        //     steps {
        //         script {
        //             sh """
        //             docker push $env.scanned_image
        //             """

        //             }
        //         }
        //     }
        }
    

    // post {
    //     always {
    //         deleteDir()
    //         sh "docker rmi $env.scanned_image"
    //     }
    // }
}