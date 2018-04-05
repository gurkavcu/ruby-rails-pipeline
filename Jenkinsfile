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
                container('docker') {
                    checkout scm   
                    def docker_id =  sh (returnStdout: true, script: 'docker ps | grep $(hostname) | grep docker | awk "{print $1}"') 
                    sh "echo \\"
                 
                    sh "docker build -t rails-example --network container:"+docker_id+" ."                
                }
            }
        }
    }
