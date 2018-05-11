node {
  stage('Checkout Source') {
    checkout scm
  }

  def azureUtil = load './deployment/jenkins/azureutil.groovy'
  def branchName = scm.branches[0].name.split('/').last()
  def targetEnv = (branchName in ['test','prod']) ? branchName : 'dev'

  stage('Prepare') {
    echo "Target environment is: ${targetEnv}"

    //Note: hard-coded the id for the service principal cred stored in Jenkins
    withCredentials([azureServicePrincipal('b3ee4c17-c53f-434a-b9b3-fc1e9390278e')]) {
      sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
    }
    
    azureUtil.prepareEnv(targetEnv)
 }

  stage('Build') {

    //Note: Maven install name in Jenkins is hard-coded here
    withMaven(maven: 'Maven')
    {
      sh("cd data-app; mvn compile; cd ..")
      sh("cd web-app; mvn compile; cd ..")
    }
  }

  stage('Test') {
    withMaven(maven: 'Maven')
    {
      sh("cd data-app; mvn test; cd ..")
      sh("cd web-app; mvn test; cd ..")
    }
  }

  stage('Deploy Function') {

      withMaven(maven: 'Maven')
      {
          // Deploy function app
          dir('function-app') {
          azureUtil.deployFunctionApp()
        }
    }
  }

  stage('Deploy Data App') {
    withMaven(maven: 'Maven', settings: 'MySettings')
    {
      withEnv(["ACR_NAME=${azureUtil.acrName}", "ACR_LOGIN_SERVER=${azureUtil.acrLoginServer}", "ACR_USERNAME=${azureUtil.acrUsername}", "ACR_PASSWORD=${azureUtil.acrPassword}"]) {
        sh("cd data-app; mvn package docker:build -DpushImage -DskipTests; cd ..")
        //sh("cd web-app; mvn package docker:build@with-new-relic -DpushImage -DskipTests; cd ..")
      }

        // Deploy data app
        withEnv(["ACR_NAME=${azureUtil.acrName}", "ACR_LOGIN_SERVER=${azureUtil.acrLoginServer}", "ACR_USERNAME=${azureUtil.acrUsername}", "ACR_PASSWORD=${azureUtil.acrPassword}"]) {
        azureUtil.deployDataApp(targetEnv, azureUtil.config.EAST_US_GROUP)
        //azureUtil.deployDataApp(targetEnv, azureUtil.config.WEST_EUROPE_GROUP)
      }
    }
  }

  stage('Deploy Web App') {

    withMaven(maven: 'Maven')
    {
     
      // Deploy web app
      dir('web-app/target') {
          azureUtil.deployWebApp(azureUtil.config.EAST_US_GROUP, "docker/Dockerfile")
          //azureUtil.deployWebApp(azureUtil.config.WEST_EUROPE_GROUP, "docker/Dockerfile")
      }
    }
  }
}