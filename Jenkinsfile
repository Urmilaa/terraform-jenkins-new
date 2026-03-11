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
                git branch: 'main', url: 'https://github.com/Urmilaa/terraform-jenkins-new.git'
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan Destroy') {
            steps {
                sh 'terraform plan -destroy'
            }
        }

        stage('Terraform Destroy') {
            when {
                expression { params.confirmDestroy == true }
            }
            steps {
                sh 'terraform destroy -auto-approve'
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
