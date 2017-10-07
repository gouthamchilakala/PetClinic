node {
   stage('Code Checkout') {
      checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'git@github.com:gouthamchilakala/PetClinic.git']]])
   }
   
   
   stage('Maven compile'){
 
      sh "mvn clean compile"

   }
   
   stage('Build'){
 
      sh "mvn package"

   }
   
 }
