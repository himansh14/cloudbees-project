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
                  rm -rf * 
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
              args '''-u 0:0
                      -p 4444:4444'''
              image 'standalone-chrome'
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
              args '-u 0:0'
            }

          }
          steps {
            sh 'echo \'mvn test -Dbrowser=firefox\''
          }
        }

      }
    }

    stage('QA') {
      agent {
        docker {
          image 'tomcat:8.0-jre8'
          args '''-u 0:0 
                  -p 8090:8090'''
        }

      }
      steps {
        unstash 'server'
        unstash 'client'
        sh '''APP_DIR=/opt/tomcat8/webapps
              rm -rf $APP_DIR/ROOT
              cp target/server.war $APP_DIR/server.war
              mkdir -p $APP_DIR/ROOT
              cp dist/* $APP_DIR/ROOT
              /opt/tomcat8/bin/startup.sh'''
        input(message: 'Deploy?', ok: 'Yes, Go ahead !')
      }
    }

    stage('Deploy') {
      steps {
        unstash 'server'
        unstash 'client'
        sh '''# deploy the exact artifacts to production 
              echo "Deploying client:"
              ls -alFh dist
              echo "Deploying server:"
              ls -alFh target'''
        echo 'Success'
      }
    }

    stage('End') {
      steps {
        echo 'End'
      }
    }

  }
}
