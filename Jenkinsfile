@Library('shared-lib-cloudbees') _


pipeline {
    agent {
        kubernetes {
            label 'my-pod-template'
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  containers:
  - name: docker
    image: docker:18.06
    command: ["cat"]
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-socket
  volumes:
  - name: docker-socket
    hostPath:
      path: /var/run/docker.sock
      type: Socket
        
"""
        }
    }
    stages {
        stage('docker') {
            steps {
                container('docker') {
                    dockerLogin()
                    
                    sh 'echo build_image'
                    sh "docker image build -t moglimcgrath/cloudbees ."
                    sh 'docker images'
                    sh 'docker version'                    
                 
                   // sh "docker run -p 8032:90 ${DOCKER_IMAGE}"
                }
            }
        }
    }
    post {
        success {
            container('docker') {
                sh "docker push moglimcgrath/cloudbees"
            }
        }
        failure {
            container('docker') {
                //sh "docker rmi moglimcgrath/cloudbees"
            }
        }
    }
}
