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
                gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                serviceName = "ruby-rails-pipeline"
                DOCKER_IMAGE_REPO = "304703668734.dkr.ecr.eu-central-1.amazonaws.com/ruby-rails-pipeline"
                container('docker') {
                    withDockerRegistry([credentialsId: 'ecr:eu-central-1:aws-cred', url: "https://${DOCKER_IMAGE_REPO}"]) {
                        sh "docker build . -t ${serviceName}:${gitCommit}"
                        sh "docker tag ${serviceName}:${gitCommit} ${DOCKER_IMAGE_REPO}:${gitCommit}"
                        sh "docker tag ${serviceName}:${gitCommit} ${DOCKER_IMAGE_REPO}:latest"
                        sh "docker push ${DOCKER_IMAGE_REPO}:${gitCommit}"
                        sh "docker push ${DOCKER_IMAGE_REPO}:latest"
                        /*slackSend color: '#4CAF50', message: "New version of ${serviceName}:${gitCommit} pushed to ECR!"*/
                    }

                }
            }
        }
    }
