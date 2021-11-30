pipeline {
   agent any
   environment {
      Access_Key = credentials('ACCESS_KEY')
      Secret_Key = credentials('SECRET_KEY')
   }
   stages {
      stage("build"){
         steps{
            echo 'build'
         }
      }

      stage("deploy"){
         steps{
            script {
               sh '(chmod 777 ./Jenkins/cfn.json )'
               sh '(sed -i -e "s|{Secret_Key}|${Secret_Key}|g; s|{Access_Key}|${Access_Key}|g" ./Jenkins/cfn.json)'
               sh '(cat ./Jenkins/cfn.json )'
               sh '(cd ${WORKSPACE};chmod 777 ./Jenkins/deploy.sh;  ./Jenkins/deploy.sh )'     
            }
         }
      }
   }
}