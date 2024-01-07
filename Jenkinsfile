pipeline {
    environment {
        imagerepo = 'shivakrishna99'
        imagename = 'nodejs-docker'
    }

    agent { label 'docker-node-1' }

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build --no-cache . -t ${imagename}:v${BUILD_NUMBER}"
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh "docker tag nodejs-docker:v${BUILD_NUMBER} ${imagerepo}/${imagename}:v${BUILD_NUMBER}"
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                sh "docker push ${imagerepo}/${imagename}:v${BUILD_NUMBER}"
            }
        }

        stage('Remove Docker Image') {
            steps {
                sh "docker rmi ${imagename}:v${BUILD_NUMBER}"
                sh "docker rmi ${imagerepo}/${imagename}:v${BUILD_NUMBER}"
            }
        }

        stage('Update Manifest') {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: 'Github_Credentials', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    withEnv(["GIT_ASKPASS=echo", "GIT_COMMITTER_EMAIL=knowledgesk9999@gmail.com", "GIT_COMMITTER_NAME=GitPracticeRepositorys"]) {
                        sh "rm -rf gitops-demo-deployment"
                        sh "git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/GitPracticeRepositorys/gitops-demo-deployment.git"
                        dir('gitops-demo-deployment') {
                            sh "sed -i 's/newTag.*/newTag: v${BUILD_NUMBER}/g' kustomize/overlays/*/*kustomization.yaml"
                            sh "git config user.email knowledgesk9999@gmail.com"
                            sh "git config user.name GitPracticeRepositorys"
                            sh "git add ${WORKSPACE}/gitops-demo-deployment/kustomize/overlays/*/*kustomization.yaml"
                            sh "git commit -m 'Update image version to: ${BUILD_NUMBER}'"
                            sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/GitPracticeRepositorys/gitops-demo-deployment.git HEAD:master -f"
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
