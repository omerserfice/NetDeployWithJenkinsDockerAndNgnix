pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'NetDeployWithJenkinsDockerAndNgnix'  // Lokal Docker imaj adı
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/omerserfice/NetDeployWithJenkinsDockerAndNgnix.git'
            }
        }

        stage('Build .NET Core') {
            steps {
                sh 'dotnet restore'
                sh 'dotnet build --configuration Release'
            }
        }

        stage('Publish .NET Core') {
            steps {
                sh 'dotnet publish -c Release -o ./publish'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    // Lokalde Docker imajını oluştur
                    docker.build("${DOCKER_IMAGE_NAME}")
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh """
                    // Eski container'ı durdur ve sil
                    docker stop ${DOCKER_IMAGE_NAME} || true
                    docker rm ${DOCKER_IMAGE_NAME} || true
                    
                    // Yeni imajı çalıştır (Nginx ile 80 portundan yayınla)
                    docker run -d --name ${DOCKER_IMAGE_NAME} -p 80:80 ${DOCKER_IMAGE_NAME}
                """
            }
        }
    }
}
