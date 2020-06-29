def aviableToProduction = null

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

        stage('build task ') {
            steps {
                script {
                    if (dockerTag.contains('-pro')) {
                        aviableToProduction = true
                    } else {
                        aviableToProduction = false
                    }
                    sh "rm ECS.tf && export TF_LOG=DEBUG && sed '1,35 s/CONTAINER_API_VAR_REPLACE/${dockerTag}/g' ecs-change > ECS.tf"
                    sh "terraform init && terraform refresh -var-file=\"envs/variables_develop.tfvars\" && terraform plan  -var-file=\"envs/variables_develop.tfvars\""
                }
            }
        }
        stage('deploy to dev') {
            steps {
                input(message : 'do you want to deploy this task to dev?')
                sh "export TF_LOG=DEBUG &&  terraform apply -input=false -auto-approve  -var-file=\"envs/variables_develop.tfvars\""
                sh "aws ecs update-service --cluster api_rest_cluster-DEV --service serviceApiRest-DEV --task-definition APIRestSmallCompany-DEV"
            }
        }

        stage('deploy to pro') {
            steps {
                script {
                    if(aviableToProduction == true){
                        sh "terraform init && terraform refresh -var-file=\"envs/variables_develop.tfvars\" && terraform plan  -var-file=\"envs/variables_develop.tfvars\""
                        input(message : 'do you want to deploy this task to pro?')
                        sh "export TF_LOG=DEBUG &&  terraform apply -input=false -auto-approve  -var-file=\"envs/variables_master.tfvars\""
                        sh "aws ecs update-service --cluster api_rest_cluster-PRO --service serviceApiRest-PRO --task-definition APIRestSmallCompany-PRO"
                    }
                }
            }
        }
        
    }
}