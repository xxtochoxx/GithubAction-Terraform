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
      REGISTRY = 't8version2020/GithubAction-Terraform'// usuario de docker hub
  }

  stages {
      stage('Hello') {
        steps {
          // First stage is a sample hello-world step to verify correct Jenkins Pipeline
          echo 'Hello World, I am Happy'
          echo 'This is my amazing Pipeline'
        }
      }
  stage('Checkout') {
        steps {
        // Get Github repo using Github credentials (previously added to Jenkins credentials)
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/xxtochoxx/GithubAction-Terraform']]])        }
      }
//   stage('Install dependencies NPM') {
//        steps {
//          sh 'npm --version'
//          echo 'ProJect ${PROJECT_ROOT}'
          //sh "cd ${PROJECT_ROOT}; 
          //sh "cd ${PROJECT_ROOT}; npm install"
 //         sh "npm install"
//        }
 //     }

     // stage('Unit tests NPM') {
       // steps {
          // Run unit tests
       //  sh "cd ${PROJECT_ROOT}; npm run test"
       // }
     // }


      //stage('Generate coverage report') {
        //steps {
          // Run code-coverage reports
          //sh "cd ${PROJECT_ROOT}; npm run coverage"
        //}
      //}
      stage('scan') {
          environment {
            // Previously defined in the Jenkins "Global Tool Configuration"
            scannerHome = tool 'sonar-scanner'
          }
          steps {
            // "sonarqube" is the server configured in "Configure System"
            withSonarQubeEnv('sonarqube') {
              // Execute the SonarQube scanner with desired flags
              sh "${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=GithubAction-Terraform/app/test \
                          -Dsonar.projectName=GithubAction-Terraform \
                          -Dsonar.projectVersion=0.0.${BUILD_NUMBER} \
                          -Dsonar.host.url=http://mysonarqube:9000 \
                          -Dsonar.sources=./${PROJECT_ROOT}/app.js,./${PROJECT_ROOT}/config/db.config.js,./${PROJECT_ROOT}/routes/developers.js \
                          -Dsonar.login=admin \
                          -Dsonar.password=#Cr1pt0m0n3d4# \
                          -Dsonar.tests=./${PROJECT_ROOT}/test \
                          -Dsonar.javascript.lcov.reportPaths=./${PROJECT_ROOT}/coverage/lcov.info"
            }
            timeout(time: 3, unit: 'MINUTES') {
              // In case of SonarQube failure or direct timeout exceed, stop Pipeline
              waitForQualityGate abortPipeline: qualityGateValidation(waitForQualityGate())
            }
          }
      }
      stage('Build docker-image') {
        steps {
          sh "cd ./${PROJECT_ROOT};docker build -t ${REGISTRY}:${BUILD_NUMBER} . "
        }
      }
      stage('Deploy docker-image') {
        steps {
          // If the Dockerhub authentication stopped, do it again
          sh 'docker login'
          sh "docker push ${REGISTRY}:${BUILD_NUMBER}"
        }
      }
  }
}
