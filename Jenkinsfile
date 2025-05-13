pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'hetulmehta/spring-petclinic-app'
        IMAGE_TAG = "${BUILD_NUMBER}" // Auto-tag with Jenkins build number
    }
    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/mehtahetul/nana-argocd.git'
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
                        sh "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Update K8s Manifest') {
            steps {
                dir('k8s') {
                    script {
                        sh """
                        sed -i 's|image: .*|image: ${DOCKER_IMAGE}:${IMAGE_TAG}|' deployment.yaml
                        git config user.name "hetulmehta"
                        git config user.email "hetulmehta20@gmail.com"
                        git add deployment.yaml
                        git commit -m "Update image to tag ${IMAGE_TAG}"
                        git push origin main
                        """
                    }
                }
            }
        }
    }
}
