pipeline {
    agent any

    environment {
        DOCKER_IMAGE  = "sidharth18/shapel-website"
        GIT_REPO_NAME = "shapel-html"
        GIT_USER_NAME = "Sidharth-420"
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout successful'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonarscanner'

                    withSonarQubeEnv('sonarqube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=shapel-static-website \
                              -Dsonar.projectName='Shapel Static Website' \
                              -Dsonar.sources=. \
                              -Dsonar.sourceEncoding=UTF-8
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                    -t ${DOCKER_IMAGE}:${BUILD_NUMBER} \
                    .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def dockerImage =
                        docker.image("${DOCKER_IMAGE}:${BUILD_NUMBER}")

                    docker.withRegistry(
                        'https://index.docker.io/v1/',
                        'docker-cred'
                    ) {
                        dockerImage.push()
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Update Deployment') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'github',
                        variable: 'GITHUB_TOKEN'
                    )
                ]) {

                    sh '''
                        git config user.email "sidharthshai98@gmail.com"
                        git config user.name "${GIT_USER_NAME}"

                        sed -i "s|image: .*|image: ${DOCKER_IMAGE}:${BUILD_NUMBER}|g" k8s/deployment.yaml

                        git add k8s/deployment.yaml

                        git commit \
                        -m "Update Shapel website image to ${BUILD_NUMBER} [skip ci]" \
                        || echo "No changes to commit"

                        git push \
                        https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}.git \
                        HEAD:main
                    '''
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
