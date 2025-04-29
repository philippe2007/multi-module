pipeline {
    options {
    timeout(time: 1, unit: 'HOURS')
    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
}
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
                         script{
                           checkSonarQualityGate ()
                        } 
                     }
                    
                }
            }
            
        }

              


        stage('Déploiement intégration') {
            //when {
            //  branch 'master'
            //  beforeOptions true
            //  beforeInput true
            //  beforeAgent true
//}
            options {
                timeout(5)
            }

            agent any
            input {
            message 'Voulez vous deployer O/N'
            ok 'OK'
            }

            steps {
                echo "Déploiement intégration "
                unstash 'Artef'
                script{
                   def DeployData = readJSON file: '/home/plb/mywork/multi-module/deployment.json'
                   def datac = DeployData["dataCenters"]
                   def integrationURL = DeployData["integrationURL"]  
                   for (DC in datac){
                    sh "mkdir -m755 -p /home/plb/${DC}"
                    sh "cp -p application/**/*.jar /home/plb/${DC}"
                   } 
                } 
                
            }
        }
     }
} 

def checkSonarQualityGate(){
    // Get properties from report file to call SonarQube 
    def sonarReportProps = readProperties  file: 'target/sonar/report-task.txt'
    def sonarServerUrl = sonarReportProps['serverUrl']
    def ceTaskUrl = sonarReportProps['ceTaskUrl']
    def ceTask

    // Get task informations to get the status
    timeout(time: 4, unit: 'MINUTES') {
        waitUntil(initialRecurrencePeriod: 1000)  {
            withCredentials ([string(credentialsId: 'SONAR_TOKEN', variable : 'token')]) {
                def response = sh(script: "curl -u ${token}: ${ceTaskUrl}", returnStdout: true).trim()
                ceTask = readJSON text: response
            }

            echo ceTask.toString()
              return "SUCCESS".equals(ceTask['task']['status'])
        }
    }

    // Get project analysis informations to check the status
    def ceTaskAnalysisId = ceTask['task']['analysisId']
    def qualitygate

    withCredentials ([string(credentialsId: 'SONAR_TOKEN', variable : 'token')]) {
        def response = sh(script: "curl -u ${token}: ${sonarServerUrl}/api/qualitygates/project_status?analysisId=${ceTaskAnalysisId}", returnStdout: true).trim()
        qualitygate =  readJSON text: response
    }

    echo qualitygate.toString()
    if ("ERROR".equals(qualitygate['projectStatus']['status'])) {
        error "Quality Gate failure"
    }
}


