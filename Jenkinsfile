pipeline {
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    agent any
    stages {
        stage('Checkout') {
            steps {
                script {
                    dir("terraform") {
                        git "https://github.com/AdiNarayana32/ec2withTerraform-Jenkins.git"
                    }
                }
            }
        }
        
        stage('Terraform Operations') {
            steps {
                script {
                    def terraformDir = "terraform"
                    dir(terraformDir) {
                        sh "terraform init"
                        sh "terraform plan -out tfplan"
                        sh "terraform show -no-color tfplan > tfplan.txt"
                        
                        // If autoApprove is set to true, apply the plan automatically
                        if (params.autoApprove) {
                            sh "terraform apply -input=false tfplan"
                        }
                    }
                }
            }
            
            when {
                expression {
                    !params.autoApprove
                }
            }
        }
        
        stage('Approval') {
            when {
                expression {
                    !params.autoApprove
                }
            }
            
            steps {
                script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }
    }
}

