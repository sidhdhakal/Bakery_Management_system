pipeline {
  agent any
  
  parameters {
    string(name: 'FRONTEND_DOCKER_TAG', defaultValue: 'latest', description: 'Frontend Docker image tag')
    string(name: 'BACKEND_DOCKER_TAG', defaultValue: 'latest', description: 'Backend Docker image tag')
  }

  environment {
    SONAR_SCANNER = tool 'Sonar' // adjust as per your configuration
    SONAR_PROJECT_KEY = "bakery-key"
    SONAR_PROJECT_NAME = "bakery-system"
  }
  
  stages {
    stage('Clone Code') {
      steps {
        git url: 'https://github.com/sidhdhakal/Bakery_Management_system.git', branch: 'main'
      }
    }

    stage('Install Dependencies') {
      steps {
        dir('frontend') {
          sh 'npm install'
        }
        dir('backend') {
          sh 'npm install'
        }
      }
    }

    stage('OWASP Dependency Check') {
      steps {
        script {
          dependencyCheck additionalArguments: '--format XML --scan ./', odcInstallation: 'OWASP'
          dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        }
      }
    }

    stage('Trivy Scan') {
      steps {
        sh 'trivy fs . > trivy-scan.txt'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('Sonar') {
          sh """
            ${SONAR_SCANNER}/bin/sonar-scanner \
            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
            -Dsonar.projectName=${SONAR_PROJECT_NAME} \
            -Dsonar.sources=. \
            -Dsonar.host.url=$SONAR_HOST_URL \
            -Dsonar.login=$SONAR_AUTH_TOKEN
          """
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 2, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }

    stage('Docker Build and Push - Frontend') {
      steps {
        dir('frontend') {
          withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh """
              docker build -t ${DOCKER_USER}/bakery-frontend:${params.FRONTEND_DOCKER_TAG} .
              echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
              docker push ${DOCKER_USER}/bakery-frontend:${params.FRONTEND_DOCKER_TAG}
            """
          }
        }
      }
    }

    stage('Docker Build and Push - Backend') {
      steps {
        dir('backend') {
          withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh """
              docker build -t ${DOCKER_USER}/bakery-backend:${params.BACKEND_DOCKER_TAG} .
              echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
              docker push ${DOCKER_USER}/bakery-backend:${params.BACKEND_DOCKER_TAG}
            """
          }
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/dependency-check-report.xml, trivy-scan.txt', allowEmptyArchive: true
    }

    success {
      build job: 'Bakery-CD',
      parameters: [
        string(name: 'FRONTEND_DOCKER_TAG', value: "${params.FRONTEND_DOCKER_TAG}"),
        string(name: 'BACKEND_DOCKER_TAG', value: "${params.BACKEND_DOCKER_TAG}")
      ]
    }
  }
}
