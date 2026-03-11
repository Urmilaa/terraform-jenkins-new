pipeline {

    agent any

    parameters {
        booleanParam(
            name: 'confirmDestroy',
            defaultValue: false,
            description: 'Confirm to destroy Terraform infrastructure'
        )
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

    stages {

        stage('Checkout Code') {
            steps {
                 dir('terraform') {
                git branch: 'main', url: 'https://github.com/Urmilaa/terraform-jenkins-new.git'
                 }
            }
        }

        stage('Terraform Init') {
            steps {
                dir('terraform') {
                sh 'terraform init'
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                dir('terraform') {
                sh 'terraform validate'
                }
            }
        }

        stage('Terraform Plan Destroy') {
            steps {
                dir('terraform') {
                    sh 'terraform plan -destroy -out=tfplan'
                    sh 'terraform show -no-color tfplan > tfplan.txt'
                }
            }
           
        }
         stage('Approval') {
             when {
                not { equals expected: true, actual: params.confirmDestroy }
            }
            steps {
                script {
                    def plan = readFile 'terraform/tfplan.txt'

                    input message: "Approve Terraform Destroy?",
                    parameters: [
                        text(name: 'Terraform Plan', defaultValue: plan, description: 'Review Destroy Plan')
                    ]
                }
            }
        }
        stage('Terraform Destroy') {
             
            when {
                expression { params.confirmDestroy == true }
            }
           steps {
                dir('terraform') {
                sh 'terraform destroy -auto-approve'
            }
            }
        }
    }

    post {
        success {
            echo 'Terraform destroy completed successfully'
        }
        failure {
            echo 'Terraform destroy failed'
        }
    }
}
