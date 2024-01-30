
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
                          -Dsonar.projectKey=ProyectoTest \
                          -Dsonar.projectName=ProyectoTest \
                          -Dsonar.projectVersion=0.0.${BUILD_NUMBER} \
                          -Dsonar.host.url=http://localhost:9000 \
                          -Dsonar.login=admin \
                          -Dsonar.password=123456 \
                          -Dsonar.sources=. \
                          -Dsonar.exclusions=vendor "
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
    
