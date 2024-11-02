pipeline {
    agent none

    stages {
        stage('Build Image with Kaniko') {
            agent {
                kubernetes {
                    yaml """
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
  labels:
    app: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
      - name: workspace-volume
        mountPath: /workspace
  volumes:
    - name: jenkins-docker-cfg
      secret:
        secretName: docker-credentials
        items:
          - key: .dockerconfigjson
            path: config.json
    - name: workspace-volume
      emptyDir: {}
"""
                }
            }
            steps {
                container('kaniko') {
                    script {
                        sh '''
                            /kaniko/executor \
                                --context "${WORKSPACE}" \
                                --dockerfile "${WORKSPACE}/Dockerfile" \
                                --destination your-registry/your-image:${BUILD_NUMBER} \
                                --destination your-registry/your-image:latest
                        '''
                    }
                }
            }
        }
    }
}
