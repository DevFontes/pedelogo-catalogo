pipeline {
    agent any

    stages {
        stage('Checkout Source') {
            steps {
                git url:'https://github.com/devfontes/pedelogo-catalogo.git', branch:'main'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerapp = docker.build("julioesf/pedelogo-catalogo:${env.BUILD_ID}",
                      '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')

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

        stage('Apply Kubernetes files') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }

            steps {
                script {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
                    sh 'cat ./k8s/api/deployment.yaml'
                }
                
                withKubeConfig([credentialsId: 'kubernetes-admin', serverUrl: 'https://masterinfra.net:6443']) {
                sh 'kubectl apply -f ./k8s/api/deployment.yaml'
                sh 'kubectl apply -f ./k8s/api/configmap.yaml'
                sh 'kubectl apply -f ./k8s/api/service.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/deployment.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/configmap.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/pv-sc.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/pv.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/pvc-sc.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/pvc.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/sc.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/secret.yaml'
                sh 'kubectl apply -f ./k8s/mongodb/service.yaml'
                }
            }
        }

        // stage('Deploy Kubernetes') {
        //     agent {
        //         kubernetes {
        //             cloud 'kubernetes'
        //         }
        //     }
        //     environment {
        //         tag_version = "${env.BUILD_ID}"
        //     }
        //     steps {
        //         script {
        //             sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
        //             sh 'cat ./k8s/api/deployment.yaml'
        //             kubernetesDeploy(configs: '**/k8s/**', kubeconfigID: 'kubernetes-admin')
        //         }
        //     }
        // }
    }
}
// script {
                //     sh 'which kubectl'
                // }
                // withKubeConfig([credentialsId: 'kubernetes-admin']) {
                    // sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
                    // sh 'chmod u+x ./kubectl' 
                    // sh 'curl --limit-rate 100k -LO "https://releases.ubuntu.com/18.04.6/ubuntu-18.04.6-desktop-amd64.iso"'
                    // sh './kubectl --insecure-skip-tls-verify --context=kubernetes apply -f ./k8s/api/deployment.yaml'