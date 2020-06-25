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
                sh "sed '1,35 s/CONTAINER_API_VAR_REPLACE/"+ $dockerTag + "/g' ecs-change > ecs.tf"
            }
        }
        
    }
}