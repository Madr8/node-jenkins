pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://host.docker.internal:8081/repository/maven-releases/'
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Madr8/node-jenkins.git'
            }
        }

        stage('Test') {
            steps {
                sh 'git --version'
                sh 'chmod +x mvnw'
                sh './mvnw --version'
                sh './mvnw clean test'
            }
        }

        stage('Build and Package') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Deploy to Nexus') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
            sh '''
            mkdir -p ~/.m2
            cat > ~/.m2/settings.xml <<EOF
<settings>
  <servers>
    <server>
      <id>nexus</id>
      <username>${NEXUS_USER}</username>
      <password>${NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
EOF

            chmod +x mvnw
            ./mvnw deploy -DskipTests
            '''
        }
    }
}
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
