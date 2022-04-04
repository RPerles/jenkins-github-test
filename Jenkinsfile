pipeline {
//    agent { docker { image 'node:16.13.1-alpine' } }
    


    environment { 
        registry = "progradius/coursdevops" 
        registryCredential = 'dockerhub_id' 
        dockerImage = '' 
    }


    agent any

    stages {
        stage('echo npm version') {
            steps {
                nodejs(nodeJSInstallationName: 'Node 16 LTS') {
                    sh 'npm --version'
                }
            }
        }
        stage('test') {
            steps {
                nodejs(nodeJSInstallationName: 'Node 16 LTS') {
                    sh 'npm install'
                    sh 'npm test'
                }
            }
        }
        stage('build') {
            steps {
                nodejs(nodeJSInstallationName: 'Node 16 LTS') {
                    sh 'npm run build'
                }
            }           
 
        }
        
        stage('build docker image') {
stage('build image') { step([$class: 'DockerBuilderPublisher', cleanImages: false, cleanupWithJenkinsJobDelete: false, cloud: 'docker', dockerFileDirectory: '', fromRegistry: [progradius/coursdevops], pushCredentialsId: 'dockerhub_id', pushOnSuccess: true, tagsString: 'xxx']) }
        }

            
        stage('Deploy our image') { 
            steps { 
                script { 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                } 
            }
        }
    }
}
