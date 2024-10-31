pipeline {
    agent none // This specifies that no static Jenkins agent is used

    stages {
        stage('Build Image with Kaniko') {
            agent {
                kubernetes {
                    // Define the Kaniko executor container
                    containerTemplate(
                        name: 'kaniko',
                        image: 'gcr.io/kaniko-project/executor:latest',
                        command: 'cat',
                        ttyEnabled: true
                    )
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
