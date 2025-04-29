pipeline {
   agent any 

    node {
    def mvnHome
    stage('Preparation') { // for display purposes
        // Get some code from a GitHub repository
         git '/home/plb/mywork/multi-module'
        // Get the Maven tool.
        // ** NOTE: This 'M3' Maven tool must be configured
        // **       in the global configuration.
        mvnHome = tool 'maven3'
    }

    stages {
        stage('Compile et tests') {
            steps {
            withEnv(["MVN_HOME=$mvnHome"]) {
                echo 'Commande Maven'
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
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

