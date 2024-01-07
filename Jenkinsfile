pipeline {
    environment {
        imagerepo = 'shivakrishna99'
        imagename = 'nodejs-docker'
        githubRepoURL = 'https://github.com/GitPracticeRepositorys/gitops-demo-deployment.git'
    }

    agent { label 'docker-node-1' }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build --no-cache . -t ${imagename}:v${BUILD_NUMBER}"
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    sh "docker tag ${imagename}:v${BUILD_NUMBER} ${imagerepo}/${imagename}:v${BUILD_NUMBER}"
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    sh "docker push ${imagerepo}/${imagename}:v${BUILD_NUMBER}"
                }
            }
        }

        stage('Remove Docker Image') {
            steps {
                script {
                    sh "docker rmi ${imagename}:v${BUILD_NUMBER}"
                    sh "docker rmi ${imagerepo}/${imagename}:v${BUILD_NUMBER}"
                }
            }
        }

        stage('Update Manifest') {
            steps {
                script {
                    // Assuming 'github_pat' is the Jenkins credentials ID for the PAT
                    withCredentials([string(credentialsId: 'pat_github', variable: 'GITHUB_PAT')]) {
                        withCredentials([usernamePassword(credentialsId: 'github_credentials', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            sh "rm -rf gitops-demo-deployment"
                            sh "git clone ${githubRepoURL}"
                            dir('gitops-demo-deployment') {
                                sh "sed -i 's/newTag.*/newTag: v${BUILD_NUMBER}/g' kustomize/overlays/*/*kustomization.yaml"
                                sh "git config user.email knowledgesk9999@gmail.com"
                                sh "git config user.name GitPracticeRepositorys"
                                sh "git add kustomize/overlays/*/*kustomization.yaml"
                                sh "git commit -m 'Update image version to: ${BUILD_NUMBER}'"
                                sh "git config --global credential.helper store"
                                sh "git remote set-url origin ${githubRepoURL}"
                                sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/****/gitops-demo-deployment.git HEAD:master -f"
                            }
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
