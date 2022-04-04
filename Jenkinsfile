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
            steps {
                dockerNode('docker') {
                sh "ls -al"
                sh "docker -version"
                sh 'docker build --force-rm -t nodejs:test .'
}
            }
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
