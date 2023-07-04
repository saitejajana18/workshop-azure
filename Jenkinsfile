import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=c1c90e3e-873d-47f3-95d7-5b71b37cbd92',
        'AZURE_TENANT_ID=e5f22e0c-04a1-4f04-97a0-79dc2427e36c']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'rs615'
      def webAppName = 'appname18'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'azure-jenkins', passwordVariable: 'LHd8Q~k7ILwbTCi5iIf9bb1txMvE.lB9E9xWtcv-', usernameVariable: '6b70ee77-232d-4e04-b72e-8e6e7cb919ce')]) {
       sh '''
          az login --service-principal -u 6b70ee77-232d-4e04-b72e-8e6e7cb919ce -p LHd8Q~k7ILwbTCi5iIf9bb1txMvE.lB9E9xWtcv- -t e5f22e0c-04a1-4f04-97a0-79dc2427e36c
          az account set -s c1c90e3e-873d-47f3-95d7-5b71b37cbd92
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g rs615 -n appname18", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war ftps://waws-prod-blu-467.ftp.azurewebsites.windows.net/site/wwwroot/webapps/ROOT.war -u appname18\$appname18:GDJBhemFjWPAAQaMTcv92SGw5AscxebRmi2vjRpcJ30loj8T0KduGxFxl7J1'"
      // log out
      sh 'az logout'
    }
  }
}
