node {
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), disableConcurrentBuilds(), [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/gkdevops/PetClinic.git/'], pipelineTriggers([githubPush()])])
    
    def MvnHome = tool name: 'MAVEN3', type: 'maven'
    def MvnCli = "${MvnHome}/bin/mvn"

    stage('Checkout Git'){
        sh 'echo "Welcome to jenkins"'
        git changelog: false, credentialsId: 'github_creds', poll: false, url: 'https://github.com/gkdevops/PetClinic.git'
    }
    
    stage('Read Maven POM'){
        readpom = readMavenPom file: '';
        def art_version = readpom.version;
        echo "${art_version}"
    }
    
    stage('Maven test'){
        sh "${MvnCli} test"
    }
    
    stage('Maven Package'){
        sh "${MvnCli} package -Dmaven.skip.test=true"
    }
    
    stage('Archive Artifacts'){
        archiveArtifacts artifacts: '**/spring-petclinic-*.war', onlyIfSuccessful: true
    }
    
    stage('Archive Junit'){
        junit '**/surefire-reports/*.xml'
    }
    
    stage('Archive Artifacts to JFROG'){
        script {
          def server = Artifactory.server 'Artifactory'
          def uploadSpec = """{
            "files": [
              {
                "pattern": "target/*.war",
                "target": "libs-release-local/PetClinic/"
              }
            ]
          }"""
          //server.upload(uploadSpec)
          server.upload spec: uploadSpec, failNoOp: true
        }
    }
    
    stage('deploy to Server'){
        ansiblePlaybook '/opt/deploy.yml'
    }
    
    stage('Smoke Test'){
        sleep 30
        sh "curl -sSf http://ec2-54-172-228-44.compute-1.amazonaws.com:8080/petclinic"
    }
}
