
//Autor - actualización : Humberto Melendez
//correo - humberto.melendez.arg@gmail.com

// Function to validate that the message returned from SonarQube is ok
def qualityGateValidation(qg) {
  if (qg.status != 'OK') {
    // emailext body: "WARNING SANTI: Code coverage is lower than 80% in Pipeline ${BUILD_NUMBER}", subject: 'Error Sonar Scan,   Quality Gate', to: "${EMAIL_ADDRESS}"
    return true
  }
  // emailext body: "CONGRATS SANTI: Code coverage is higher than 80%  in Pipeline ${BUILD_NUMBER} - SUCCESS", subject: 'Info - Correct Pipeline', to: "${EMAIL_ADDRESS}"
  return false
}
pipeline {
  agent any

  environment {
        SONARQUBE_HOST = credentials('http://mysonarqube:9000')
        SONARQUBE_TOKEN = credentials('123456')
        DEFECTDOJO_API_KEY = credentials('b6060080172b75aa5b4698f225926402ad1cefc3')
    }

  tools {
      nodejs 'nodejs'
  }
  stages {
      stage('First steps pipeline') {
        steps {
          // First stage is a sample hello-world step to verify correct Jenkins Pipeline
          echo 'Hello World, I am Happy'
          echo 'This is my Pipeline'
        }
      }
  stage('Checkout branch') {
        steps {
        // Get Github repo using Github credentials (previously added to Jenkins credentials)
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/xxtochoxx/GithubAction-Terraform']]])        }
      }
  
      stage('sonar-scanner') {
          environment {
            // Previously defined in the Jenkins "Global Tool Configuration"
            def scannerHome = tool 'sonar-scanner';
          }
          steps {
            // "sonarqube" is the server configured in "Configure System"
            withSonarQubeEnv('sonarqube') {
              // Execute the SonarQube scanner with desired flags
              sh "${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=ProyectoTestv2 \
                          -Dsonar.projectName=ProyectoTestv2 \
                          -Dsonar.projectVersion=0.0.${BUILD_NUMBER} \
                          -Dsonar.host.url=http://mysonarqube:9000 \
                          -Dsonar.login=admin \
                          -Dsonar.password=123456 \
                          -Dsonar.sources=. \
                          -Dsonar.exclusions=vendor "
            }

          }
      }

      stage('Send SonarQube to DefectDojo') {
            steps {
                // Enviar resultados de SonarQube a DefectDojo
                script {
                    withCredentials([string(credentialsId: 'defectdojo-api-key', variable: 'DEFECTDOJO_API_KEY')]) {
                        sh 'defectdojo-send-results --sonarqube-host $SONARQUBE_HOST --sonarqube-token $SONARQUBE_TOKEN --defectdojo-api-key $DEFECTDOJO_API_KEY --sonarqube-report sonarqube-report.json'
                    }
                }
            }

      stage('Boletin') {
        steps {
            script{
              sh 'echo "Notificacion enviada"'
              sh 'echo "Reporte generado"'
            }
          
        }
             
      }
  }
    post {
        always {
            // Puedes agregar acciones posteriores, como enviar notificaciones o limpiar recursos temporales
            script{
              sh 'echo "Limpiar recursos temporales"'
            }
        }
    }
        

      }
    
