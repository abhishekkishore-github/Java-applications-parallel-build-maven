pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        git branch: 'main', url: 'github-repo-url'
      }
    }
    stage('Build'){
      parallel{
        stage('Build Version 1'){
          agent {
            docker {
              image 'yogendrakokamkar/maven-java-11:latest'
              args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
            }
          }
          steps {
            sh 'ls -ltr'
            sh 'cd spring-boot-app && mvn clean package'
            sh 'pwd'
            sh 'cp spring-boot-app/target/spring-boot-web.jar /var/lib/jenkins/build1'
          }
        }
        stage('Build Version 2'){
          agent {
            docker {
              image 'yogendrakokamkar/maven-java-11:latest'
              args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
            }
          }
          steps {
            sh 'ls -ltr'
            sh 'cd spring-boot-app-2 && mvn clean package'
            sh 'cp spring-boot-app-2/target/spring-boot-web.jar /var/lib/jenkins/build2'
          }
        }
      }
    }
    stage('Build and Push Docker Image 1')
    {
      environment {
        DOCKER_IMAGE = "your-dockerhub-username/springboot-maven:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker')
      }
      steps {
        script {
            sh 'pwd'
            sh 'cd ../parallel_maven@2/spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker") {
                dockerImage.push()
            }
        }
      }
    }
    stage('Build and Push Docker Image 2')
    {
      environment {
        DOCKER_IMAGE = "your-dockerhub-username/springboot-maven-2:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker')
      }
      steps {
        script {
            sh 'cd ../parallel_maven@2/spring-boot-app-2 && docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker") {
                dockerImage.push()
            }
        }
      }
    }
    stage('Update Deployment File 1 and Deploy V1') {
        agent{
          node{
            label 'kubernetes'
          }
        }
        environment {
            GIT_REPO_NAME = "your-github-repo-name"
            GIT_USER_NAME = "your-github-username"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "your-github-email-id"
                    git config user.name "your-github-username"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s,your-dockerhub-username/springboot-maven:.*,your-dockerhub-username/springboot-maven:${BUILD_NUMBER},g" spring-boot-manifests/deployment.yml
                    git add spring-boot-manifests/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    kubectl apply -f spring-boot-manifests/deployment.yml
                    kubectl apply -f spring-boot-manifests/service.yml
                '''
            }
        }
    }
    stage('Update Deployment File 2 and Deploy V2') {
        agent{
          node{
            label 'kubernetes'
          }
        }
        environment {
            GIT_REPO_NAME = "your-github-repo-name"
            GIT_USER_NAME = "your-github-username"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "your-github-email-id"
                    git config user.name "your-github-username"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s,your-dockerhub-username/springboot-maven-2:.*,your-dockerhub-username/springboot-maven-2:${BUILD_NUMBER},g" spring-boot-manifests-2/deployment.yml
                    git add spring-boot-manifests-2/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    kubectl apply -f spring-boot-manifests-2/deployment.yml
                    kubectl apply -f spring-boot-manifests-2/service.yml
                '''
            }
        }
    }
  }
}
