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
                            sh "terraform init"
                            sh "terraform apply -var-file='../../env/dev.tfvars' -auto-approve"
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
                    dir('terraform-infrastructure') {
                        sh "terraform apply -var-file='../../env/dev.tfvars' -auto-approve"
                        sh "aws s3 cp ../build --recursive ${APPLICATION_NAME_DEV}"
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
