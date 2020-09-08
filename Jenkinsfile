pipeline {
    agent {
        label 'worker-1'
    }

    tools {
        maven 'MAVEN3'
	jdk 'JAVA8'
	}
    
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
        disableConcurrentBuilds()
    }
    
    parameters {
        choice choices: ['poc', 'dev', 'sit', 'uat'], description: 'environment name', name: 'environment'
        choice choices: ['master'], description: 'Branch Name', name: 'BRANCH'
    }
    
    stages {
        stage('Checkout Code'){
            steps {
                deleteDir()
                git branch: '$BRANCH', credentialsId: 'github-creds', url: 'https://github.com/gopishank/PetClinic.git'
            }
        }
	stage('Unit Tests'){
	    steps {
		    sh "mvn test"
		}
	}
	stage('SonarQube Scan'){
	    environment {
	        SCANNER_HOME = tool 'SonarQubeScanner'
	    }
	    steps {
	        withSonarQubeEnv(credentialsId: 'sonarqube-creds', installationName: 'SonarQube') {
	            sh "$SCANNER_HOME/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
	        }
	    }
	}
	stage('Maven Package'){
	    steps {
	        sh "mvn package"
	    }
	}
	stage('Upload Artifacts'){
	    steps {
	        sh '''
	        curl -u admin:admin POST "http://ec2-3-237-45-132.compute-1.amazonaws.com:8081/service/rest/v1/components?repository=petclinic" -H "accept: application/json" -H "Content-Type: multipart/form-data" -F "maven2.groupId=org.springframework.samples" -F "maven2.artifactId=petclinic" -F "maven2.version=${BUILD_ID}.0.0" -F "maven2.asset1=@${WORKSPACE}/target/petclinic.war" -F "maven2.asset1.extension=war"
	        '''
	    }
	}
	stage('Deploy Code'){
	    steps {
	        ansiblePlaybook installation: 'ANSIBLE29', playbook: '/home/ubuntu/playbooks/deploy.yaml'
	    }
	}
	stage('Curl Test'){
	    steps {
	        sleep 5
	        sh "curl http://ec2-54-162-51-156.compute-1.amazonaws.com:8080/petclinic"
	    }
	}
	stage ('Send Notification'){
	    steps {
	        sh ""
	    }
	}
   }
}
