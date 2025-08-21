pipeline {
  agent any

  options { timestamps() }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv('Sonarqube') { // Name you configured under Manage Jenkins → System → SonarQube servers
          script {
            def extraProps = [
              'sonar.projectKey=juice-shop-fork',
              'sonar.projectName=OWASP Juice Shop (Fork)',
              "sonar.projectVersion=${env.GIT_COMMIT ?: 'local'}",
              'sonar.sources=.',
              'sonar.sourceEncoding=UTF-8',
              'sonar.scm.provider=git',
              // keep SAST focused on source, skip builds & tests & assets
              'sonar.exclusions=**/node_modules/**,**/dist/**,**/build/**,**/*.spec.ts,**/*.spec.js,**/test/**,**/e2e/**,**/*.md,frontend/src/assets/i18n/**,data/static/i18n/**,**/*.png,**/*.jpg,**/*.gif,**/*.svg,**/*.ico'
            ].join(' -D')

            docker.image('sonarsource/sonar-scanner-cli:latest').inside("-v ${pwd()}:/usr/src") {
              sh """sonar-scanner -D${extraProps}"""
            }
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          // Works best if you also create a SonarQube → Jenkins webhook: http(s)://<jenkins>/sonarqube-webhook/
          waitForQualityGate abortPipeline: false
        }
      }
    }
  }

  post {
    always { echo 'Scan complete. Check results in SonarQube.' }
  }
}
