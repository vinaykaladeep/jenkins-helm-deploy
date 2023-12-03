pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        } 
        stage('copy artifacts') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
        stage('Unit tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    def customImage = docker.build("vinaykaladeep/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }
        stage('build on kubenetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'pwd'
                    sh 'cp -R helm/* .'
                    sh 'ls -ltrh'
                    sh 'pwd'
                    sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=vinaykaladeep/petclinic --set image.tag=${BUILD_NUMBER}'
                }
            }
        }
    }
}