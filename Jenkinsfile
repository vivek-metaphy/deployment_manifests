pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yaml '''
      apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kubectl
    image: joshendriks/alpine-k8s
    command:
    - /bin/cat
    tty: true    
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: regcred
        items:
          - key: .dockerconfigjson
            path: config.json'''
    }
  }

  stages {

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=registry.digitalocean.com/metaphy:${BUILD_NUMBER}
            '''
          }
        }
      }
    }
  
  }
}