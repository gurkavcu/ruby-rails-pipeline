podTemplate(label: 'builder',
            containers: [
                    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true),
                    containerTemplate(name: 'kubectl', image: 'ceroic/kubectl', command: 'cat', ttyEnabled: true),
                    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)
            ],
            volumes: [
                    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
                    /*secretVolume(secretName: 'maven-settings', mountPath: '/root/.m2'),*/
                    secretVolume(secretName: 'kubeconfig', mountPath: '~/.kube')
                    /*persistentVolumeClaim(claimName: 'mavenrepo-volume-claim', mountPath: '/root/.m2nrepo')*/
            ]) {

        node('builder') {

            stage('Run kubectl') {
              container('kubectl') {
                sh "kubectl get pods"
              }
            }
            stage('Run helm') {
              container('helm') {
                sh "helm list"
              }
            }

            stage('Build docker image') {
                checkout scm
                def GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                container('docker') {
                    def SERVICE_NAME = "ruby-rails-pipeline"
                    def DOCKER_IMAGE_REPO = "304703668734.dkr.ecr.eu-central-1.amazonaws.com/ruby-rails-pipeline"
                    def DOCKER_ID =  sh (returnStdout: true, script: 'docker ps | grep $(hostname) | grep docker | awk \'{print $1}\'') 

                    sh """
                       docker ps
                       echo ${hostname}
                       docker build . -t ${SERVICE_NAME}:${GIT_COMMIT} --network container:${DOCKER_ID}
                       docker tag ${SERVICE_NAME}:${GIT_COMMIT} ${DOCKER_IMAGE_REPO}:${GIT_COMMIT}
                       docker tag ${SERVICE_NAME}:${GIT_COMMIT} ${DOCKER_IMAGE_REPO}:latest
                       """

                    withDockerRegistry([credentialsId: 'ecr:eu-central-1:aws-cred', url: "https://${DOCKER_IMAGE_REPO}"]) { 
                        sh "docker push ${DOCKER_IMAGE_REPO}:${GIT_COMMIT}"
                        /*slackSend color: '#4CAF50', message: "New version of ${serviceName}:${gitCommit} pushed to ECR!"*/
                    }
                }
            }

        }
    }
