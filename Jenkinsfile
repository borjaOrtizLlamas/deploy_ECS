def variablesDef = null

pipeline {
   agent any
    parameters {
        string(defaultValue: "latest", description: 'the tag name of the container is going to be deploy', name: 'dockerTag')
    }
   	stages {
        stage('dowload variables') {
            steps {
                dir('envs') {
                    git credentialsId: 'github_credential', url: 'https://github.com/borjaOrtizLlamas/shop_infraestucture_generator_vars.git'
                }
            }
        }



        stage('execute terraform') {
            steps {
                script {
                    if (dockerTag.contains('beta')) {
                        variablesDef = 'develop'
                    } else {
                        variablesDef = 'master'
                    }

                    sh "sed '1,35 s/CONTAINER_API_VAR_REPLACE/${dockerTag}/g' ecs-change > ECS.tf"
                    sh "export TF_LOG=DEBUG  && terraform init && terraform refresh -var-file=\"envs/variables_${variablesDef}.tfvars\" && terraform plan  -var-file=\"envs/variables_${variablesDef}.tfvars\""
                    input(message : 'do you want to deploy this build to dev?')
                }
            }
        }
        stage('deploy terraform') {
            steps {
                sh "export TF_LOG=DEBUG &&  terraform apply -input=false -auto-approve  -var-file=\"envs/variables_${variablesDef}.tfvars\""
            }
        }

        
    }
}