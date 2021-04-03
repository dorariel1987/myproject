#!/usr/bin/env groovy

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

		stage('Docker Build & Push') {
			container('docker') {
				echo "cloning from github"
				git branch: 'dev', url: 'https://github.com/dorariel1987/myproject.git'
				echo "Building consumer & producer docker images"
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
           echo " adding git & curl"
           sh "apk add --no-cache curl git && curl -sLS cli.openfaas.com | sh"
	       echo "adding helm push plugin"
	       sh "helm plugin install https://github.com/chartmuseum/helm-push.git"
           sh "helm repo add chartmuseum http://192.168.49.2:32688"
	       sh "helm repo update"
	       echo "packaging helm charts"
	       sh "helm package consumer-chart/"
	       sh "helm package producer-chart/"
	       echo "helm push charts"
	       sh "helm push --force producer-chart/ chartmuseum"
	       sh "helm push --force consumer-chart/ chartmuseum"
               sh "helm repo add bitnami https://charts.bitnami.com/bitnami"
               sh "helm uninstall rabbitmq"
               sh "helm install rabbitmq bitnami/rabbitmq --set service.type=NodePort"
               sh "helm upgrade -i consumer consumer-chart/."
               sh "helm upgrade -i producer producer-chart/."
            }
        }
    }
}
