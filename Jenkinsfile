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
  labels:
    some-label: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    command:
    - /kaniko/executor
    args:
    - --dockerfile=/workspace/Dockerfile
    - --context=dir:///workspace
    - --no-push
    volumeMounts:
    - name: workspace-volume
      mountPath: /workspace
  volumes:
  - name: workspace-volume
    emptyDir: {}
"""
                }
            }
            steps {
                container('kaniko') {
                    sh 'echo "Building Docker image with Kaniko"'
                }
            }
        }
    }
}
