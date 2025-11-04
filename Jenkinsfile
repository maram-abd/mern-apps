pipeline {
    agent any
    triggers { pollSCM('H/5 * * * *') }

    environment {
        IMAGE_SERVER = 'maram02025/mern-server'  // ← Remplacez par votre username Docker Hub
        IMAGE_CLIENT = 'maram02025/mern-client'  // ← Remplacez par votre username Docker Hub
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'git@github.com:maram-abd/mern-apps.git',
                credentialsId: 'github_ssh'
            }
        }

        stage('Build+Push SERVER') {
            when { changeset 'server/**' }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                    echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                    docker build -t $IMAGE_SERVER:${BUILD_NUMBER} server
                    docker push $IMAGE_SERVER:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Build+Push CLIENT') {
            when { changeset 'client/**' }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                    echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                    docker build -t $IMAGE_CLIENT:${BUILD_NUMBER} client
                    docker push $IMAGE_CLIENT:${BUILD_NUMBER}
                    '''
                }
            }
			
        }
		
		stage('Security Scan') {
             steps {
              sh '''
             docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \\
             aquasec/trivy image maram02025/mern-server:${BUILD_NUMBER} > trivy_report.txt
             echo "=== RAPPORT TRIVY COMPLET ==="
            cat trivy_report.txt
            '''
           }
       }
    }
    post {
        always {
            sh 'docker system prune -af || true'
        }
    }
}