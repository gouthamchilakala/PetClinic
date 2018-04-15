pipeline {
    agent any
    stages {
        stage ('Checkout Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/gouthamchilakala/PetClinic.git']]])
            }
        }
        stage ( 'Compile') {
            steps {
                sh '/opt/apache-maven-3.5.3/bin/mvn compile'
            }
        }
        stage ( ' Test Cases') {
            steps {
                sh '/opt/apache-maven-3.5.3/bin/mvn test'
            }
        }
        stage ( ' Package' ) {
            steps {
                sh '/opt/apache-maven-3.5.3/bin/mvn package'
            }
        }
    }
}
