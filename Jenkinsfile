def label = "docker-jenkins-${UUID.randomUUID().toString()}"
def home = "/home/jenkins"
def workspace = "${home}/workspace/build-docker-jenkins"
def workdir = "${workspace}/src/localhost/docker-jenkins/"

def ecrRepoName = "my-jenkins"
def tag = "$ecrRepoName:latest"

podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:alpine'),
    containerTemplate(name: 'helm', image: 'alpine/helm:3.5.3', command: 'cat', ttyEnabled: true)
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {
    node('mypod') {

		stage('Docker Build') {
			container('docker') {
				echo "cloning from github"
				git branch: 'dev', url: 'https://github.com/dorariel1987/myproject.git'
				echo "Building docker image..."
				sh 'docker build -f consumer/Dockerfile -t dorariel1987/consumer:latest ./consumer/'
				sh 'docker build -f producer/Dockerfile -t dorariel1987/producer:latest ./producer/'
	            withDockerRegistry(credentialsId: 'docker') {
	                sh 'docker push dorariel1987/consumer:latest'
	                sh 'docker push dorariel1987/producer:latest'
	            }
	            sh 'docker images'
			}
		}


        stage('do some helm work') {
            container('helm') {

               sh "apk add --no-cache curl git && curl -sLS cli.openfaas.com | sh"
               sh "curl http://192.168.49.2:32688/index.yaml"
               sh "helm repo add bitnami https://charts.bitnami.com/bitnami"
               sh "helm uninstall rabbitmq"
               sh "helm upgrade -i rabbitmq bitnami/rabbitmq --set service.type=NodePort"
               sh "helm upgrade -i consumer consumer-chart/."
               sh "helm upgrade -i producer producer-chart/."
            }
        }
    }
}
