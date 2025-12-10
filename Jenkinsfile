pipeline {
    agent any
environment {
     gcpCreds = 'gcp_credentials'
     dockerCreds = credentials('dockerhub_login')
     registry = "${dockerCreds_USR}/vatcal"
     registryCredentials = "dockerhub_login"
     dockerImage = ""
     TF_VAR_gcp_project = "qwiklabs-gcp-04-bdad45405328"
     TF_VAR_docker_registry = "${registry}"
 }

stages {
     stage('Run Tests') {
     steps {
         sh 'npm install'
         sh 'npm test'
     }
 }
 stage('Build Image') {
     steps {
         script {
         dockerImage = docker.build(registry)
         }
     }
}
 stage('Push Image') {
     steps {
         script {
         docker.withRegistry("", registryCredentials) {
             dockerImage.push("${env.BUILD_NUMBER}")
             dockerImage.push("latest")
         }
         }    
     }    
 }

stage('Clean Up') {
     steps {
     sh "docker image prune --all --force --filter 'until=48h'" 
     }
 }
 stage('Provision Server') {
     steps {
         script {
             withCredentials([file(credentialsId: gcpCreds, variable: 
            'GCP_CREDENTIALS')]) {
             sh '''
            export GOOGLE_APPLICATION_CREDENTIALS=$GCP_CREDENTIALS
             terraform init
            terraform apply -auto-approve
             '''
             }
         }
     }

   }
 }

}


    
    // stages {
    //     stage('Checkout'){
    //         steps {
    //           git url: 'https://github.com/fadavick/vat-calculator.git',
    //               branch: 'main'

    //         }
    //     }
        // stage('Build') {
        //     steps {
        //       sh 'cp -f bashrc ~/.bashrc'
        //       npm 'install'
        //       npm 'run build'

        //    }
        // }
        // stage('Archive') {
        //     steps {
        //       sh 'tar -czf build.tar.gz build'
        //       archiveArtifacts 'build.tar.gz'
        //     }
        // }
//     }
// }
