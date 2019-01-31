node {
    def mvnHome = tool name: 'Maven_3.6', type: 'maven'
    def mvnCli = "${mvnHome}/bin/mvn"
    
    properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '10', numToKeepStr: '')),
        disableConcurrentBuilds(),
        parameters([string(defaultValue: 'DEV', description: 'This is a parameter in Jenkins and it can be altered during the Job execution.', name: 'environment', trim: false)]),
        pipelineTriggers([pollSCM('H/10 * * * *')])
    ])
    
    stage('Checkout SCM'){
        git branch: 'master', credentialsId: 'github-creds', url: 'https://github.com/gouthamchilakala/PetClinic'
    }
    stage('Read praram'){
        echo "The environment chosen during the Job execution is ${params.environment}"
        echo "$JENKINS_URL"
    }
    stage('maven compile'){
        // def mvnHome = tool name: 'Maven_3.6', type: 'maven'
        // def mvnCli = "${mvnHome}/bin/mvn"
        sh "${mvnCli} clean compile"
    }
    stage('maven package'){
        sh "${mvnCli} package -Dmaven.test.skip=true"
    }
    stage('Archive atifacts'){
        archiveArtifacts artifacts: '**/*.war', onlyIfSuccessful: true
    }
    stage('Archive Test Results'){
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
    stage('Deploy Tomcat'){
        sh 'curl -u tomcat:tomcat -T **/*.war "http://ec2-3-85-15-36.compute-1.amazonaws.com:8080/manager/text/deploy?path=/&update=true"'
    }
    stage('Smoke Test'){
        sleep 5
        sh "curl http://ec2-3-85-15-36.compute-1.amazonaws.com:8080/"
    }

}
