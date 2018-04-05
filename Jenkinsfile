podTemplate(label: 'builder',
            containers: [
                    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true),
                    containerTemplate(name: 'kubectl', image: 'ceroic/kubectl', command: 'cat', ttyEnabled: true),
                    containerTemplate(name: 'docker', image: 'docker:18.03.0-ce', command: 'cat', ttyEnabled: true, args: '--net=host')
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
                    sh "docker build -t rails-example ."
                
                }
            }
        }
    }
