pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME = "register-app-pipeline"
        IMAGE_TAG = "1.0.${BUILD_NUMBER}" // Define IMAGE_TAG if used in sed
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/nyanhtetkyaw/gitops'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "Before update:"
                    cat deployment.yaml
                    sed -i 's|${APP_NAME}:.*|${APP_NAME}:${IMAGE_TAG}|g' deployment.yaml
                    echo "After update:"
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        git config --global user.name "j_nhk"
                        git config --global user.email "nyanhtetkyaw@gmail.com"
                        git add deployment.yaml
                        git commit -m "Updated Deployment Manifest" || echo "No changes to commit"
                        git push https://${USERNAME}:${PASSWORD}@github.com/nyanhtetkyaw/gitops main
                    """
                }
            }
        }
    }
}
