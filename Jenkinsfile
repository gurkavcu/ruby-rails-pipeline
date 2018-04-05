podTemplate(label: 'builder',
            containers: [
                    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true),
                    containerTemplate(name: 'kubectl', image: 'ceroic/kubectl', command: 'cat', ttyEnabled: true)
            ],
            volumes: [
                    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
                    /*secretVolume(secretName: 'maven-settings', mountPath: '/root/.m2'),*/
                    secretVolume(secretName: 'kubeconfig', mountPath: '~/.kube')
                    /*persistentVolumeClaim(claimName: 'mavenrepo-volume-claim', mountPath: '/root/.m2nrepo')*/
            ]) {

        node('builder') {

            stage('Checkout') {
                checkout scm
            }

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

            /*stage('Build docker image') {
                gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                DOCKER_IMAGE_REPO = "<YOUR-AWS-ECR-ID>.dkr.ecr.eu-west-1.amazonaws.com/<SERVICE-REPO-NAME>"
                container('docker') {
                    withDockerRegistry([credentialsId: 'ecr:eu-west-1:AWS ECR', url: "https://${DOCKER_IMAGE_REPO}"]) {
                        sh "docker build . -t ${serviceName}:${gitCommit}"
                        sh "docker tag ${serviceName}:${gitCommit} ${DOCKER_IMAGE_REPO}:${gitCommit}"
                        sh "docker tag ${serviceName}:${gitCommit} ${DOCKER_IMAGE_REPO}:latest"
                        sh "docker push ${DOCKER_IMAGE_REPO}:${gitCommit}"
                        sh "docker push ${DOCKER_IMAGE_REPO}:latest"
                        slackSend color: '#4CAF50', message: "New version of ${serviceName}:${gitCommit} pushed to ECR!"
                    }

                }
            }*/
        }
    }
