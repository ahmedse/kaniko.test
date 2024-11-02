pipeline {
    agent none

    environment {
        NEXUS_URL = 'nexus.sherkety.info'
        REPO_PATH = 'repository/sherkety.net-repo'
        IMAGE_NAME = 'mykaniko.test'
        NEXUS_CREDS = credentials('sherkety.net-nexususer-creds')
    }

    stages {
        stage('Build Image with Kaniko') {
            agent {
                kubernetes {
                    yaml """
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: workspace-volume
        mountPath: /workspace
      - name: kaniko-config
        mountPath: /kaniko/.docker/
  volumes:
    - name: workspace-volume
      emptyDir: {}
    - name: kaniko-config
      emptyDir: {}
"""                    
                }
            }
            steps {
                container('kaniko') {
                    // Create Docker config for Nexus authentication
                    sh '''
                        echo "{
                          \\"auths\\": {
                            \\"${NEXUS_URL}\\": {
                              \\"auth\\": \\"$(echo -n ${NEXUS_CREDS_USR}:${NEXUS_CREDS_PSW} | base64)\\"
                            }
                          }
                        }" > /kaniko/.docker/config.json
                    '''
                    
                    // Build and push image
                    sh '''
                        /kaniko/executor \
                            --context "${WORKSPACE}" \
                            --dockerfile "${WORKSPACE}/Dockerfile" \
                            --destination ${NEXUS_URL}/${REPO_PATH}/${IMAGE_NAME}:${BUILD_NUMBER} \
                            --destination ${NEXUS_URL}/${REPO_PATH}/${IMAGE_NAME}:latest
                    '''
                }
            }
        }
    }
}
