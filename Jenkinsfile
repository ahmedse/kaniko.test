pipeline {
    agent none // Specifies that no static Jenkins agent is used

    stages {
        stage('Build Image with Kaniko') {
            agent {
                kubernetes {
                    // Define the Kubernetes pod and Kaniko executor container using YAML
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
    - pwd
    tty: true
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
                // Execute Kaniko command to build the Docker image
                container('kaniko') {
                    sh '''
                    /kaniko/executor --dockerfile=${WORKSPACE}/Dockerfile --context=dir://${WORKSPACE} --no-push
                    '''
                }
            }
        }
    }
}
