pipeline {
    agent { label 'jenkinslave' }
    
    stages {
        
        stage('Build') { 
            steps {
                sh "mvn install -DskipTests" 
            }
        }
        stage('Create docker image') { 
            steps {
                script {
                    def scmVars = checkout([
                        userRemoteConfigs: [[
                            url: 'https://github.com/ykyunjung/helidon_jenkins_oke.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                sh "sudo docker build -f Dockerfile -t ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:latest ." 
                }
            }
        }
        stage('Push image to OCIR') { 
            steps {
                script {
                    def scmVars = checkout([
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/ykyunjung/helidon_jenkins_oke.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                sh "sudo docker login -u ${params.REGISTRY_USERNAME} -p '${params.REGISTRY_TOKEN}' ${params.DOCKER_REGISTRY}"
                sh "sudo docker tag ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:latest ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:latest"
                sh "sudo docker push ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:latest" 
                }
               }
            }
        }
}
