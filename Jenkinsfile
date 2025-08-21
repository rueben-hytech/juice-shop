pipeline {
  agent any

  options {
    timestamps()
  }

  tools {
    // optional but recommended for JS/TS analysis context resolution
    nodejs 'NodeJs'                 // <-- define in Global Tool Configuration (optional)
  }

  environment {
    // Name must match the SonarQube server you added in Jenkins > System
    SONARQUBE_SERVER = 'Sonarqube'
    // Tweak exclusions if you want more/less surface
    SONAR_EXCLUSIONS = '**/node_modules/**,**/dist/**,**/build/**,**/*.spec.ts,**/*.spec.js,**/test/**,**/e2e/**,**/*.md,frontend/src/assets/i18n/**,data/static/i18n/**'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv("${SONARQUBE_SERVER}") {
          sh """
            sonar-scanner \
              -Dsonar.projectKey=juice-shop-fork \
              -Dsonar.projectName='OWASP Juice Shop (Fork)' \
              -Dsonar.sources=. \
              -Dsonar.exclusions=${SONAR_EXCLUSIONS} \
              -Dsonar.sourceEncoding=UTF-8
          """
        }
      }
    }

    stage('Quality Gate') {
      steps {
        script {
          // Fails the build if the quality gate is not passed
          timeout(time: 10, unit: 'MINUTES') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
              error "Pipeline failed due to Quality Gate: ${qg.status}"
            }
          }
        }
      }
    }
  }

  post {
    always {
      echo "Scan complete. See results in SonarQube."
    }
  }
}
