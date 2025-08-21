pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'master',
            url: 'https://github.com/<your-username>/juice-shop.git',
            credentialsId: 'Github-API-Token' // or remove if public
      }
    }

    stage('SonarQube Scan (Tool)') {
      steps {
        withSonarQubeEnv('Sonarqube') {
          script {
            def scannerHome = tool 'SonarScanner'
            sh """
              "${scannerHome}/bin/sonar-scanner" \
                -Dsonar.projectKey=juice-shop-fork \
                -Dsonar.projectName='OWASP Juice Shop (Fork)' \
                -Dsonar.projectVersion=${env.GIT_COMMIT ?: 'local'} \
                -Dsonar.sources=. \
                -Dsonar.sourceEncoding=UTF-8 \
                -Dsonar.scm.provider=git \
                -Dsonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/*.spec.ts,**/*.spec.js,**/test/**,**/e2e/**,**/*.md,frontend/src/assets/i18n/**,data/static/i18n/**
            """
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 30, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: false
        }
      }
    }
  }
}
