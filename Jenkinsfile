pipeline {
  agent any

  tools {
    // Optional but recommended for TypeScript rules that need Node.
    nodejs 'NodeJs'  // Define this under Manage Jenkins » Tools
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SonarQube Scan') {
      steps {
        script {
          def scannerHome = tool 'SonarScanner'   // Manage Jenkins » Tools name
        }
        withSonarQubeEnv('Sonarqube') {           // Manage Jenkins » System name
          sh """
              echo "[DEBUG] scannerHome=${scannerHome}
              ls -l ${scannerHome}/bin || true
              ${scannerHome}/bin/sonar-scanner \
              -X \
              -Dsonar.verbose=true \
              -Dsonar.projectKey=rueben-hytech_juice-shop_2f91e1c1-dd03-4da9-8339-01f516660cce \
              -Dsonar.projectName='OWASP Juice Shop (Fork)' \
              -Dsonar.sources=. \
              -Dsonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/*.spec.ts,**/*.spec.js,**/test/**,**/e2e/**,**/*.md,frontend/src/assets/i18n/**,data/static/i18n/** \
              -Dsonar.sourceEncoding=UTF-8
          """
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 30, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
  }
}
