pipeline {
    agent any

    stages {
        stage('Checkout Source') {
            steps {
                git url:'https://github.com/DevFontes/pedelogo-catalogo.git', branch:'main'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerapp = docker.build("DevFontes/pedelogo-catalogo:{$env.BUILD_ID}",
                    '-f .srcPedeLogo.Catalogo.Api.Dockerfile .')

                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                    }                    
                }
            }
        }
    }
}


docker pull julioesf/pedelogo-catalogo:v1.0.0