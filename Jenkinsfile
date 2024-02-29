node {
    def application = "devopsexample"
    def dockerhubaccountid = "suraj438" // Your Docker Hub username

    def mvnHome = tool 'maven-3.9.6' // Maven version 3.9.6

    def dockerImage

    def dockerImageTag = "${dockerhubaccountid}/${application}:${env.BUILD_NUMBER}"

    stage('Clone Repo') {
        git url: 'https://github.com/SD1435/Docker-Project.git', branch: 'main'
        mvnHome = tool 'maven-3.9.6' // Using Maven 3.9.6 for this project
    }

    stage('Build Project') {
        sh "'${mvnHome}/bin/mvn' clean install"
    }

    stage('Build Docker Image with new code') {
        dockerImage = docker.build("${dockerhubaccountid}/${application}:${env.BUILD_NUMBER}")
    }

    stage('Push Image to Remote Repo') {
        echo "Docker Image Tag Name ---> ${dockerImageTag}"
        docker.withRegistry('', 'dockerhub') {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push("latest")
        }
    }

    stage('Remove running container with old code') {
        sh "docker rm -f \$(docker ps -a -f name=devopsexample -q) || true"
    }

    stage('Deploy Docker Image with new changes') {
        sh "docker run --name devopsexample -d -p 2222:2222 ${dockerhubaccountid}/${application}:${env.BUILD_NUMBER}"
    }

    stage('Remove old images') {
        sh("docker rmi ${dockerhubaccountid}/${application}:latest -f")
    }
}
