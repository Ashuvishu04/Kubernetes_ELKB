node('master'){

    stage('CleanWs'){
        cleanWs()
    }
    stage('GitClone'){
        git branch: "$branch_name", credentialsId: '3d128102-570c-4fa4-9e73-576def939e8d', url: 'https://github.com/WynkLimited/mitm'
    }
    stage('build'){
        sh """
	    PATH=$PATH:/opt/apache-maven-3.3.9/bin/
        mvn clean package -Dspring.profiles.active=docker
        """
    }
    stage('Login to ap-south-1 ECR'){
      sh '$(aws ecr get-login --no-include-email --region ap-south-1)'
    }
    stage('createDockerFile'){
        writeFile file: 'Dockerfile', text: dockerfileData
    }
    stage('BuildDockerImage'){
        sh """
        docker build --build-arg version=\${projectVersion} . --tag \${docker_registry}/\${imagename}:\${service_name}\${BUILD_NUMBER}
        docker push \${docker_registry}/\${imagename}:\${service_name}\${BUILD_NUMBER}
        """
    }
    stage('ECS Deployment'){
    // env.new_docker_image=docker_registry+"/"+imagename+":"+service_name+env.BUILD_NUMBER
         sh """
             NEW_DOCKER_IMAGE=\${docker_registry}/\${imagename}:\${service_name}\${BUILD_NUMBER}
             TASK_DEF_OLD=\$(aws ecs describe-task-definition --task-definition  \$task_family --region \$region)
             TASK_DEF_NEW=\$(echo \$TASK_DEF_OLD | jq --arg NDI \$NEW_DOCKER_IMAGE '.taskDefinition.containerDefinitions[0].image=\$NDI')
             TASK_FINAL=\$(echo \$TASK_DEF_NEW | jq '.taskDefinition|{memory: .memory, cpu: .cpu, placementConstraints: .placementConstraints,family: .family, volumes: .volumes, containerDefinitions: .containerDefinitions}')
             FINAL_TASK_FOR_ROLLBACK=\$(echo \$TASK_DEF_OLD | jq '.taskDefinition|{memory: .memory, cpu: .cpu, placementConstraints: .placementConstraints,family: .family, volumes: .volumes, containerDefinitions: .containerDefinitions}')
             echo -n \$FINAL_TASK_FOR_ROLLBACK > FINAL_TASK_FOR_ROLLBACK
             aws ecs register-task-definition --family \$task_family --region \$region --cli-input-json "\$(echo \$TASK_FINAL)"
             aws ecs update-service --service \$service_name --region \$region --task-definition \$task_family --cluster \$cluster_name
         """
     }
     stage('Verifying Deployment'){
         println "Waiting service to reached steady-state"
         try{
             sh "aws ecs wait services-stable --region \$region --cluster \$cluster_name --services \$service_name"
             rollback=false
         }
         catch (Exception e){
             //deployment failed, do rollback
             rollback=true
         }
         finally{
             //leaving it empty for future use
         }
     }
     stage('test-phase'){
        // build job: 'wynk-api-automation', parameters: [string(name: 'Branch_Name', value: 'master'), string(name: 'service', value: 'atv'), string(name: 'environment', value: 'prod'), string(name: 'build', value: 'all'), string(name: 'job_name', value: job),[$class: 'StringParameterValue', name: 'branch_deployed', value: branch_name]], propagate: false, wait: false, quietPeriod: 5
     }
     if (rollback){
           stage ('rollback deployment'){
               println "Ready for deployment roll back"
               sh """
               aws ecs register-task-definition --region \$region --family \$task_family --cli-input-json "\$(cat FINAL_TASK_FOR_ROLLBACK)"
               aws ecs update-service --region \$region --service \$service_name --task-definition \$task_family --cluster \$cluster_name
               """
           }
       }
     if (rollback){
         error 'Deployment failed: Had to rollback'
     }
}
