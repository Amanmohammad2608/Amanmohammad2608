pipeline {
   agent any


   stages {
       stage('Code Quality') {
           steps {
               echo 'Sonar Analysis Started'
               sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://98.81.204.37:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_9c68a24b100cdc1f18ea6c833741f47e6eb23394" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lmstest'
               echo 'Sonar Analysis Completed'
           }
       }
      
       stage('Build LMS') {
           steps {
               echo 'LMS Build Started'
               sh 'cd webapp && npm install && npm run build'
               echo 'LMS Build Completed'
           }
       }
      
       stage('Publish LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                   sh "curl -v -u admin:134113114 --upload-file webapp/lms-${packageJSONVersion}.zip http://98.81.204.37:8081/repository/lms/"
               }
           }
       }
      
       stage('Deploy LMS') {
           steps {
               script {
                   def packageJson = readJSON file: 'webapp/package.json'
                   def packageJSONVersion = packageJson.version
                   echo "${packageJSONVersion}"
                   sh "curl -u admin:134113114 -X GET \'http://98.81.204.37:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                   sh 'sudo rm -rf /var/www/html/*'
                   sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                   sh "sudo cp -r webapp/dist/* /var/www/html"
               }
           }
       }
   }
}
