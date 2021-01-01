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
                        git credentialsId: 'gitlab-https', url: 'https://gitlab.com/poc-aws/terraform-infrastructure.git'

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
                        }

                        sh "terraform apply -var-file='${WORKSPACE}/env/dev.tfvars' -auto-approve"
                        sh "aws s3 cp ../build ${APPLICATION_NAME_DEV} --recursive"
                    }
                }
            }
            stage ('Deploy HML') { 
                steps {
                    sh "echo skip"
                }
            }
            stage('Confirm deploy PRD') {
                steps {
                    input 'Prosseguir para PRD?'
                }
            }
            stage ('Deploy PRD') { 
                steps {
                    sh "echo skip"
                }
            }
        }           
}
