pipeline {
    agent any
    
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
                        $class: 'GitSCM',
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/mangdan/helidon_jenkins_oke.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                sh "docker build -f Dockerfile -t ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:${scmVars.GIT_COMMIT} ." 
                }
            }
        }
        stage('Push image to OCIR') { 
            steps {
                script {
                    def scmVars = checkout([
                        $class: 'GitSCM',
                        doGenerateSubmoduleConfigurations: false,
                        userRemoteConfigs: [[
                            url: 'https://github.com/mangdan/helidon_jenkins_oke.git'
                          ]],
                        branches: [ [name: '*/master'] ]
                      ])
                sh "docker login -u ${params.REGISTRY_USERNAME} -p '${params.REGISTRY_TOKEN}' ${params.DOCKER_REGISTRY}"
                sh "docker tag ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:${scmVars.GIT_COMMIT} ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:${scmVars.GIT_COMMIT}"
                sh "docker push ${params.DOCKER_REGISTRY}/${params.DOCKER_REPO}:${scmVars.GIT_COMMIT}" 
                env.GIT_COMMIT = scmVars.GIT_COMMIT
                sh "export GIT_COMMIT=${env.GIT_COMMIT}"
                }
               }
            }
        }
}
