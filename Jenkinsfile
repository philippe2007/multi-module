pipeline {
    options {
    timeout(time: 1, unit: 'HOURS')
    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
}
   agent none

    stages {
        stage('Compile et tests') {
            agent {
                kubernetes {
                inheritFrom 'jdk17-agent'
            }
}
            steps {
                container(name:openjdk-17)
                echo 'Commande Maven'
                sh "./mvnw -Dmaven.test.failure.ignore=true clean package"
                //createTarGz sourceDir:'application/src/main/', extensions:['xml','java'],outputDir:'Archives' 
            } 
        post {
            always {
            // One or more steps need to be included within each condition's block.
            junit '**/target/surefire-reports/*.xml'
             }
            success {
            // One or more steps need to be included within each condition's block.
            archiveArtifacts 'application/**/*.jar'
            archiveArtifacts 'Archives/**/*.tar.gz'
            stash includes: 'application/**/*.jar', name: 'Artef'
            }
            unsuccessful {
            // One or more steps need to be included within each condition's block.
             mail bcc: '', body: 'Merci de regarder le pipeline multi module ', cc: '', from: '', replyTo: 'jenkins@plb.com', subject: 'Erreur lors buils pipeline multimodule', to: 'philippe.sellam@bnpparibas.com'
             }
        }           
        }
     }
 
} 
