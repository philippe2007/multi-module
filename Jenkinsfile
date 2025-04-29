pipeline {
   agent any 


    stages {
        stage('Compile et tests') {
            tools {
                 maven 'maven3'
            }
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
    
}

