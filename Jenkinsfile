import groovy.json.JsonSlurper

 
 

 
def getFtpPublishProfile(def publishProfilesJson) {

 
def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)

 
for (p in pubProfiles)

 
if (p['publishMethod'] == 'FTP')

 
return [url: p.publishUrl, username: p.userName, password: p.userPWD]

 
}

 
 

 
node {

 
withEnv(['AZURE_SUBSCRIPTION_ID=bbd5c64b-8672-4465-b5d5-4c98ab6e4041',

 
'AZURE_TENANT_ID=0adb040b-ca22-4ca6-9447-ab7b049a22ff',

 
'AZURE_CLIENT_ID=ab8350a6-6673-49a4-894a-1b568a91d0c8',

 
'AZURE_CLIENT_SECRET=33RI7-OYGLsJFhRLSOruoNvQdJYtq~RxRW'

 
]) {

 
stage('init') {

 
checkout scm

 
}

 
 

 
stage('build') {

 
sh 'mvn clean package'

 
}

 
 

 
stage('deploy') {

 
def resourceGroup = 'QuickstartJenkins-rg'

 
def webAppName = 'appshruti'

 
// login Azure

 
withCredentials([usernamePassword(credentialsId: 'AzureServicePrincipal', passwordVariable: '33RI7-OYGLsJFhRLSOruoNvQdJYtq~RxRW', usernameVariable: 'ab8350a6-6673-49a4-894a-1b568a91d0c8')]) {

 
sh '''

 
az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID

 
az account set -s $AZURE_SUBSCRIPTION_ID

 
'''

 
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
