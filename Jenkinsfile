import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=ce1381ea-5897-4241-8847-a6e240e2fc69',
        'AZURE_TENANT_ID=1b3c4a2d-e9a4-4bfb-8cdd-f11d94d07b77']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
   stage('deploy') {
      def resourceGroup = 'workshop-2'
      def webAppName = 'workshop-2'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'uDK8Q~VkzP3~eSQv~I0YJ085iLqf~u-KwXjcGaIf', passwordVariable: '20def07e-df93-4a4d-8d9e-f7752aa8adb1', usernameVariable: 'workshop-2')]) {
       
         // sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
          sh az login --service-principal --username 'workshop-2' --password '20def07e-df93-4a4d-8d9e-f7752aa8adb1' --tenant '1b3c4a2d-e9a4-4bfb-8cdd-f11d94d07b77'
          az account set -s $AZURE_SUBSCRIPTION_ID
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
