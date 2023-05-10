pipeline {
  agent any 
  stages {
    stage('Server') {
      steps {
        sh '''echo "Building the server code.."
              mvn -version
              mkdir -p target
              touch "target/server.war"'''
      }
    }
  }
}
