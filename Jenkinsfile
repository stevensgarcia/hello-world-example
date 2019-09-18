node('docker') {
  
  stage('Poll') {
    /* Checkout latest push */
    scm checkout
  }
  stage('Build & Unit test') {
    /* Build and unit test */
    sh 'mvn clean verify -DskipITs=ture';
    /* Publish unit test reports */
    junit '**/target/surefire-reports/TEST-*.xml'
    /* Package the build */
    archiveArtifacts 'target/*.jar'
  }
  stage('Static Code Analysis') {
    sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
  }
  stage('Integration Test') {
    /* Run Integration tests */
    sh 'mvn clean verify -Dsurefire.skip=true';
    /* Publish Integration tests reports */
    junit '**/target/failsafe-reports/TEST-*.xml'
    archiveArtifacts 'target/*.jar'
  }
  stage('Publish') {
    def server = Artifactory.server 'Default Artifactory Server'
    def uploadSpec = """{
      "files": [
        { 
          "pattern": "target/hello-0.0.1.war", "target":
          "example-project/${BUILD_NUMBER}/", "props":
          "Integration-Tested=Yes;Performance-Tested=No"
        }
      ]
      }"""
    server.upload(uploadSpec)
  }
  stage('Poll') {
  }

}
