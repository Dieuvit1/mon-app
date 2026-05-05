pipeline {
    agent any

    environment {
        GITHUB_REPO = 'git@github.com:Dieuvit1/mon-app.git'
        APP_NAME = 'mon-app'
        GITHUB_USER = 'Jenkins CI'
        GITHUB_EMAIL = 'jenkins@ci.local'
        SSH_CRED_ID = 'github-ssh-key'
        IMAGE_NAME = 'mon-app-ci'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Export HTML Static') {
            steps {
                sh '''
                    docker create --name build-container-${BUILD_NUMBER} ${IMAGE_NAME}:${BUILD_NUMBER}
                    docker start -a build-container-${BUILD_NUMBER}
                    docker cp build-container-${BUILD_NUMBER}:/app/out ./out
                    docker rm build-container-${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy GitHub Pages') {
            steps {
                sshagent(credentials: ["${SSH_CRED_ID}"]) {
                    sh '''
                        cd out
                        touch .nojekyll
                        git init
                        git config user.name "${GITHUB_USER}"
                        git config user.email "${GITHUB_EMAIL}"
                        git checkout -b gh-pages
                        git add .
                        git commit -m "deploy: publication GitHub Pages build ${BUILD_NUMBER}"
                        git remote add origin ${GITHUB_REPO}
                        git push -f origin gh-pages
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Déploiement réussi : https://Dieuvit1.github.io/mon-app/"
        }
        failure {
            echo "Pipeline échoué. Vérifier la Console Output."
        }
    }
}