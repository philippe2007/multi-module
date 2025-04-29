pipeline {
   agent none
    tools {
         maven 'maven3'
         jdk 'java21'
     }
environment{
    SONAR_TOKEN = credentials('SONAR_TOKEN')
} 

    stages {
        stage('Compile et tests') {
            agent any
            steps {
                echo 'Commande Maven'
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
        } 
        post {
            always {
            // One or more steps need to be included within each condition's block.
            junit '**/target/surefire-reports/*.xml'
             }
            success {
            // One or more steps need to be included within each condition's block.
            archiveArtifacts 'application/**/*.jar'
            stash includes: 'application/**/*.jar', name: 'Artef'
            }
            unsuccessful {
            // One or more steps need to be included within each condition's block.
             mail bcc: '', body: 'Merci de regarder le pipeline multi module ', cc: '', from: '', replyTo: 'jenkins@plb.com', subject: 'Erreur lors buils pipeline multimodule', to: 'philippe.sellam@bnpparibas.com'
             }
        }           
        }
        stage('Analyse qualité et vulnérabilités') {
            parallel {
                stage('Vulnérabilités') {
                    agent any
                    steps {
                        echo 'Tests de Vulnérabilités OWASP'
                        sh "mvn -DskipTests verify"
                    }
                    
                }
                 stage('Analyse Sonar') {
                     agent any
                     steps {
                        echo 'Analyse sonar'
                        sh 'mvn -Dsonar.token=${SONAR_TOKEN} clean integration-test sonar:sonar'
                     }
                    
                }
            }
            
        }
            
        stage('Déploiement intégration') {
            agent any
            input {
  message 'Dans quel Data Center, voulez-vous déployer l’artefact ?'
  parameters {
    text defaultValue: '''Paris
Lille
Lyon''', name: 'VILLE'
  }
}

            steps {
                echo "Déploiement intégration"
                unstash 'Artef'
                sh 'mkdir -m755 -p /home/plb/${VILLE}'
                sh 'cp -p ${Arte} /home/plb/${VILLE}/${Artef}'
            }
        }
     }
}

