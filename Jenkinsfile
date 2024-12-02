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
        // stage('Extract Repository Name') {
        //     steps {
        //         script {
        //             def gitUrl = env.GIT_URL
        //             def userRepo = gitUrl.replaceAll(/https:\/\/[^\/]+\//, '').replace('.git', '')
        //             echo "User/Repo: ${userRepo}"
        //             env.repo_name = userRepo
        //         }
        //     }
        // }
        // stage('Scan SCA with Prisma') {
        //     steps {
        //         script {
        //             withCredentials([usernamePassword(credentialsId: 'prisma-pink', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
        //                 docker.image('bridgecrew/checkov:latest').inside("--entrypoint=''") {
        //                     try {
        //                         sh """
        //                             checkov -d . --bc-api-key ${TL_USER}::${TL_PASS} --repo-id ${env.repo_name} --branch main --use-enforcement-rules
        //                         """
        //                         echo "Checkov scan passed without violations."
        //                     } catch (Exception e) {
        //                         echo "Checkov scan failed due to enforcement rule violations."
        //                         error("Build failed due to Checkov enforcement rule violations.")
        //                     }
        //                 }
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
        //             withCredentials([usernamePassword(credentialsId: 'prisma-pink', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
        //                 def command = """
        //                 /apps/devsecops/prisma/twistcli images scan --address $env.PRISMA_CONSOLE_URL \
        //                 -u $TL_USER \
        //                 -p $TL_PASS \
        //                 --details $env.scanned_image
        //                 """ 

        //                 sh command
        //             }
        //         }
        //     }
        // }
  

        // stage('Push image to Harbor') {
        //     steps {
        //         script {
        //             def imageName = "hubdc.dso.local/${env.scanned_image}"
            
        //             sh """
        //                 docker tag ${env.scanned_image} ${imageName}
        //                 docker push ${imageName}
        //             """

        //         }
        //     }
        // }

        stage('Deploy Workload to Openshift Cluster') {
            steps {
                script {     
                    withCredentials([file(credentialsId: 'OCP_LAB', variable: 'KUBECONFIG_FILE')]) {
                        sh """
                            export KUBECONFIG=$KUBECONFIG_FILE
                            
                            # Verify connection
                            oc whoami
                            
                            # Set the OpenShift project
                            # oc project my-namespace
                            
                            # Deploy the image
                            # oc set image deployment/my-deployment my-container=hubdc.dso.local/${env.scanned_image} --record
                            
                            # Ensure the rollout succeeds
                            # oc rollout status deployment/my-deployment
                        """
                    }

                }
            }
        }
    }
    

    post {
        always {
            deleteDir()
            sh "docker rmi $env.scanned_image || true" 
        }
    }
}
