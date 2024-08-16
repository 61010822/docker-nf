pipeline {
    agent {label 'slave01'}

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'application_security', description: 'Name for the Docker image')
    }
    environment {
        TAG_NAME = "$BUILD_NUMBER"
        PRISMA_CONSOLE_URL = "https://asia-southeast1.cloud.twistlock.com/aws-singapore-961149791"
        PRISMA_API_URL = "https://api.sg.prismacloud.io/"
    }

    stages {
        stage('Extract Repository Name') {
            steps {
                script {
                    def gitUrl = env.GIT_URL
                    def userRepo = gitUrl.replaceAll(/https:\/\/[^\/]+\//, '').replace('.git', '')
                    echo "User/Repo: ${userRepo}"
                    env.repo_name = userRepo
                }
            }
        }
        stage('Scan SCA with Prisma') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'prisma-pink', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
                        docker.image('bridgecrew/checkov:latest').inside("--entrypoint=''") {
                            sh "checkov -d . --bc-api-key ${TL_USER}::${TL_PASS} --repo-id ${env.repo_name} --branch main --use-enforcement-rules"
                        }
                    }
                }
            }
        }
        // stage('Authentication') {
        //     steps {
        //         script {
        //             authenticate = {
        //                 withCredentials([usernamePassword(credentialsId: 'prisma-pink', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
        //                     def response = sh(
        //                         script: """curl -s -w '%{http_code}' -L -X POST '${PRISMA_API_URL}login' \
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
        //                 script: """curl -w '%{http_code}' -L -X POST '${PRISMA_API_URL}code/api/v1/repositories' \
        //                     -H 'Content-Type: application/json' \
        //                     -H 'Accept: application/json' \
        //                     -H 'Authorization: Bearer $token' \
        //                     --data-raw '{ 
        //                     "type": "github",
        //                     "data": ["$env.repo_name"]
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
                    withCredentials([usernamePassword(credentialsId: 'prisma-pink', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
                        def command = """
                        /apps/devsecops/prisma/twistcli images scan --address $env.PRISMA_CONSOLE_URL \
                        -u $TL_USER \
                        -p $TL_PASS \
                        --details $env.scanned_image
                        """ 

                        sh command
                    }
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
    }
    

    post {
        always {
            deleteDir()
            // sh "docker rmi $env.scanned_image"
        }
    }
}