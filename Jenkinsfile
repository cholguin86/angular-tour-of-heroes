pipeline{
    agent any
    options {
      timestamps()
    }
    
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        nodejs "node"
    }
    
    environment {
      PATH_SONAR = tool"sonarqube-scanner"
      PROJECT_KEY = "angular_pilot"
      EXCLUSIONS = "**/*.scss,**/node_modules/**,**/*.spec.ts,**/*.conf.js,**/e2e/**,**/*.json"	  
    }
    

    stages{
        stage('Build') {
            steps {
              echo "Building..."
              sh '''              
              rm -rf package-lock.json			  
			  npm install -y			  
              '''
			  stash "node_modules"
            }
        }
        stage('Unit Test') {
            steps {
              echo "Building..."
			  unstash "node_modules"
              sh '''              			  
              npm run ng test --no-watch
              '''
            }
        }
        stage('SonarQube') {    
            agent any   
            steps {
              unstash "node_modules"
			  sh '''
              npm run coverage
              '''
			  withSonarQubeEnv("sonarqube-scanner") {
                sh "${PATH_SONAR}/bin/sonar-scanner \
                -Dsonar.projectKey=${PROJECT_KEY} \
                -Dsonar.projectVersion=1 \
                -Dsonar.scm.disabled=true \
                -Dsonar.exclusions=${EXCLUSIONS} \
                -Dsonar.sources=src \
                -Dsonar.sourceEncoding=UTF-8 \
				-Dsonar.javascript.lcov.reportPaths=coverage/angular-tour-of-heroes/lcov.info"
              }                       
            }
        }       
    }
    post{
        success{
            echo "Excelente trabajo"                        
        }
        failure{
            echo "Fallo el proceso"                                                         
        }
    }
}