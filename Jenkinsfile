pipeline {
   agent any 
    tools {
        maven 'maven3'
        jdk 'java21'
    }


    stages {
        stage('Compile et tests') {

            steps {
            
            
                echo 'Commande Maven'
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            
        }     
        }
        stage('Analyse qualité et vulnérabilités') {
            parallel {
                stage('Vulnérabilités') {
                    steps {
                        echo 'Tests de Vulnérabilités OWASP'
                    }
                    
                }
                 stage('Analyse Sonar') {
                     steps {
                        echo 'Analyse sonar'
                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {

            steps {
                echo "Déploiement intégration"

                
            }
        }

     }
post {
  always {
    // One or more steps need to be included within each condition's block.
        junit '**/target/surefire-reports/*.xml'
  }
  success {
    // One or more steps need to be included within each condition's block.
    archiveArtifacts 'application/**/*.jar'
  }
  failure {
    // One or more steps need to be included within each condition's block.
    emailext body: 'Merci de vérifier le pipeline dans jenkins file', subject: 'Erreur dans Pipeline', to: 'philippe.sellam@bnpparibas.com'
    }
}


}

