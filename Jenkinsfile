pipeline { 
    agent { label "node-terraform-agent"  }
    options { skipDefaultCheckout() } 
        stages { 
            stage('Checkout') {
                steps {
                    sh 'printenv'
                    checkout scm
                }
            }
            stage('Setup Terraform Remote State') {
                steps {
                    dir('terraform-infrastructure') {
                        git credentialsId: 'gitlab-https', url: 'https://github.com/andersondarioo/poc-terraform-infrastructure.gitt'

                        dir('remote-state') {
                            catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                                sh "terraform init"
                                sh "terraform apply -var-file='${WORKSPACE}/env/dev.tfvars' -auto-approve"
                            }
                        }
                    }
                }
            }
            stage ('Build') { 
                steps {
                    sh "npm install"
                    sh "npm run build"
                }
            }
            stage ('Test') { 
                steps {
                    sh "echo skip"
                }
            }
            stage ('Deploy DEV') { 
                steps {
                    dir("terraform-infrastructure/applications/${APPLICATION_INFRA_NAME}") {
                        catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                            sh "terraform init"
                            sh "terraform workspace new dev"
                            sh "terraform workspace select dev"
                        }

                        sh "terraform apply -var-file='${WORKSPACE}/env/dev.tfvars' -auto-approve"
                        sh "aws s3 cp ${WORKSPACE}/build s3://${APPLICATION_NAME}-dev --recursive"
                    }
                }
            }
            stage ('Deploy HML') { 
                steps {
                    dir("terraform-infrastructure/applications/${APPLICATION_INFRA_NAME}") {
                        catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                            sh "terraform init"
                            sh "terraform workspace new hml"
                            sh "terraform workspace select hml"
                        }

                        sh "terraform apply -var-file='${WORKSPACE}/env/hml.tfvars' -auto-approve"
                        sh "aws s3 cp ${WORKSPACE}/build s3://${APPLICATION_NAME}-hml --recursive"
                    }
                }
            }
            stage('Confirm deploy PRD') {
                steps {
                    input 'Prosseguir para PRD?'
                }
            }
            stage ('Deploy PRD') { 
                steps {
                    dir("terraform-infrastructure/applications/${APPLICATION_INFRA_NAME}") {
                        catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                            sh "terraform init"
                            sh "terraform workspace new prd"
                            sh "terraform workspace select prd"
                        }

                        sh "terraform apply -var-file='${WORKSPACE}/env/prd.tfvars' -auto-approve"
                        sh "aws s3 cp ${WORKSPACE}/build s3://${APPLICATION_NAME}-prd --recursive"
                    }
                }
            }
        }           
}
