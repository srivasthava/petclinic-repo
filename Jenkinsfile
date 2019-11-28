//noinspection GroovyAssignabilityCheck
pipeline {
    agent any
    tools {
        jdk 'jdk8'
        maven 'maven'
    }
    stages {
        stage('Build') {
            steps {
                // git branch: 'master', url: 'https://github.com/ovkhasch/petclinic-repo'
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Build docker images') {
            steps {
                PETCLINIC_VERSION = sh (
                  script: 'xf=`ls *.jar` && echo $xf | awk \'{print substr($1,1,match($1,/.[^.]*$/)-1)}\' | awk \'{print substr($1,match($1,/[0-9]/))}\'', 
                  returnStdout: true
                ).trim()
                echo "Petclinic version: ${PETCLINIC_VERSION}"
                
                script {
                    docker.withRegistry('https://registry.kaiburr.com', 'chef01-registry') {
                        def petclinicImage = docker.build('petclinic:${PETCLINIC_VERSION}', '.')
                        petclinicImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'petclinic-repo/target/*.jar'
        }
    }
}
