pipeline {

   triggers {
    pollSCM('*/1 * * * *')
   }

    agent {
      kubernetes {

yaml '''
kind: Pod
spec:
  containers:

  - name: node
    image: node
    command:
    - cat
    tty: true

  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: docker-registry-key
        mountPath: /kaniko/.docker
      - name: endpoints
        mountPath: /kaniko/endpoints
    resources:
      limits:
        memory: 2Gi

  volumes:
  - name: docker-registry-key
    projected:
      sources:
      - secret:
          name: docker-registry-key
          items:
            - key: .dockerconfigjson
              path: config.json
  
  - name: endpoints
    configMap:
      name: endpoints
'''
        defaultContainer 'kaniko'
      }
    }

    options {
        buildDiscarder logRotator(
                    daysToKeepStr: '16',
                    numToKeepStr: '10'
            )
        disableConcurrentBuilds()
    }

    stages {

        //Main Branch
        stage('Build and Deploy Main Branch ') {

            environment {
                PROJECT_NAME = "${JOB_NAME.split("/")[2]}"

                REPO_URL = 'https://github.com/RPerles/jenkins-github-test'

                DOCKER_REPO = 'progradius/coursdevops'
                DOCKER_IMAGE = "${DOCKER_REPO}:latest"

            }

            stages {

              stage('Cloning Repository') {

                steps {

                    checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    userRemoteConfigs: [[credentialsId: 'github', url:"${REPO_URL}"]]])
                }
              }

              stage('Building Image') {

                steps {

                    echo 'Building Image'

                    sh '''/kaniko/executor -f ./Dockerfile -c `pwd` --verbosity info --cache=false --destination=${DOCKER_IMAGE}'''


                }
              }

            }

            post {

                success {

                    echo 'Success'

                }

                failure {

                    echo 'Failure'

                }
            }
        }
    }
}
