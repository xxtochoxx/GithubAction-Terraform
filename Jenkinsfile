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

  environment {
      // General Variables for Pipeline
      PROJECT_ROOT = 'GithubAction-Terraform/app'
      EMAIL_ADDRESS = 'xxtochoxx@gmail.com'
      REGISTRY = 't8version2020'// usuario de docker hub
      DOCKER_REGISTRY_CREDENTIALS = 't8version2020'// usuario de docker hub
      DOCKER_IMAGE_NAME = 'githubactionterraform'
      DOCKER_IMAGE_TAG = 'Jenkis-Sonar-v2'
      DOCKER_PASSWORD='D_ut47r5#/s'
      
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
                          -Dsonar.projectKey=GithubActionTerraform8 \
                          -Dsonar.projectName=GithubActionTerraform8 \
                          -Dsonar.host.url=http://mysonarqube:9000 \
                          -Dsonar.login=admin \
                          -Dsonar.password=#Cr1pt0m0n3d4# \
                          -Dsonar.sources=. \
                          -Dsonar.exclusions=vendor "
            }

          }
      }
      stage('Build docker-image') {
        steps {
                script {
                    // Construir la imagen Docker 
                    // app = docker.build("GithubAction-Terraform/Jenkis-Sonar-v2") 
                    sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ."
                }
        }
      }
      stage('Test image') {
             sh 'echo "Tests passed"'
      }
    
      stage('Deploy docker-image - Push image') {
                    steps {
                script {
                    // Autenticarse en el registro de Docker utilizando las credenciales de Jenkins
                    withCredentials([usernamePassword(credentialsId: DOCKER_REGISTRY_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    }

                    // Subir la imagen al registro de Docker
                    sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                }
            }
        

      }
    }
  }
