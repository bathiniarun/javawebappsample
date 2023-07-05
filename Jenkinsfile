import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=2d402a4e-590e-45b3-86a3-0e43c31d40ed',
        'AZURE_TENANT_ID=b3eff091-3a6e-47ad-b094-ee66ddf1a558']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'Arun'
      def webAppName = 'Dev-1-main'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'azure-1-pipeline', passwordVariable: 'eQ08Q~UHR53CHvMmReHOvwB2gytfOP_ey00uQbVH', usernameVariable: '2d402a4e-590e-45b3-86a3-0e43c31d40ed')]) {
       sh '''
          az login --service-principal -u 1b04da5c-ff0d-48a5-85c4-a54d2a4bb079 -p eQ08Q~UHR53CHvMmReHOvwB2gytfOP_ey00uQbVH -t b3eff091-3a6e-47ad-b094-ee66ddf1a558
          az account set -s 2d402a4e-590e-45b3-86a3-0e43c31d40ed
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g Arun -n Dev-1-main", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
