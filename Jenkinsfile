pipeline {
  agent any
  stages {
    stage('Server') {
      parallel {
        stage('Build') {
          agent {
            docker {
              image 'maven'
            }

          }
          steps {
            sh '''echo "Building the server code.."
              rm -rf * . 
              mvn -version
              mkdir -p target
              touch "target/server.war"'''
            stash(name: 'server', includes: '**/*.war')
          }
        }

        stage('Client') {
          agent {
            docker {
              image 'node:6'
              args '-u 0:0'
            }

          }
          steps {
            sh '''echo "Building the client code..."
npm install --save react 
mkdir -p dist 
cat > dist/index.html <<EOF 
hello !
EOF
touch "dist/client.js"'''
            stash(name: 'client', includes: '**/dist/*')
          }
        }

      }
    }

    stage('Chrome') {
      parallel {
        stage('Test') {
          agent {
            docker {
              image 'selenium/standalone-chrome'
            }

          }
          steps {
            sh 'echo \'mvn test -Dbrowser=chrome\''
          }
        }

        stage('Firefox') {
          agent {
            docker {
              image 'selenium/standalone-firefox'
            }

          }
          steps {
            sh 'echo \'mvn test -Dbrowser=firefox\''
          }
        }

      }
    }

  }
}