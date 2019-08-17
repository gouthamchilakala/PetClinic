node('master'){
    
    def MvnHome = tool name: 'MAVEN3', type: 'maven'
    def MvnCli = "${MvnHome}/bin/mvn"
    
    properties([[$class: 'JiraProjectProperty'], buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '10', numToKeepStr: '')), disableConcurrentBuilds(), durabilityHint('PERFORMANCE_OPTIMIZED'), parameters([string(defaultValue: 'DEV', description: 'This is env value', name: 'env', trim: false)]), pipelineTriggers([cron('30 18 * * *')])])
    
    stage('Checkout Code'){
        git credentialsId: 'github.com', url: 'https://github.com/gkdevops/PetClinic.git'
    }
    
    stage('Read Variable'){
        sh "echo ${params.env}"
    }
    
    stage('Maven Test'){
        sh "${MvnCli} test"
    }

    stage('Maven Package'){
        sh "${MvnCli} package -Dmaven.skip.test=true"
    }
    
    stage('Archive Artifactory'){
            script {
              def server = Artifactory.server 'artifactory'
              def uploadSpec = """{
                "files": [
                  {
                    "pattern": "target/*.war",
                    "target": "libs-release-local/"
                  }
                ]
              }"""
              server.upload(uploadSpec)
            }
    }
}
