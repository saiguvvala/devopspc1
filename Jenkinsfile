pipeline {
   agent {
      dockerfile {
         filename 'Jenkins/Dockerfile-Build'
         args '-u root'
      }
   }

   environment {
      GITHUB_TOKEN          = credentials('Jenkins-User')
      AWS_REGION            = "eu-west-2"
      AWS_DEFAULT_REGION    = "eu-west-2"
      PROJECT_NAME          = "ALB-DB"
   }
      
   stages {
      stage('Clean') {
         steps {
            sh '(cd ${WORKSPACE}/src; make clean;)'
         }
      }

      stage('Install') {
         steps {
            sh 'git config --global url."https://${GITHUB_TOKEN}:x-oauth-basic@github.com/".insteadOf "https://github.com/"'
         }
      }
      stage('Analyse') {
         steps {
            parallel(
               "CFN Linting": {
                  sh '(cd ${WORKSPACE}; make cfn-lint)'
               },
            )
         }
      }
     
      stage('Deploy') {
         parallel {
            stage('Development') {
               when { branch 'master' }
               steps {
                  script {
                     sh '(cd ${WORKSPACE}; ./Jenkins/deploy.sh )'
                  }
               }
            }
         }
      }
   }
   //Cleanup
   post {
      cleanup {
         cleanWs()
      }
   }
}