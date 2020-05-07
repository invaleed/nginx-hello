def project = 'research'
def appName = 'nginx-hello'
def imageTag = "192.168.65.180:5000/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

pipeline {
  agent {
    kubernetes {
      label 'nginx-hello'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
#  serviceAccountName: cd-jenkins
  containers:
  - name: docker
    image: docker:dind
    securityContext:
      privileged: true    
    tty: true
    command: ["dockerd-entrypoint.sh", "--insecure-registry=192.168.65.180:5000"]
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
	stages {
	
	stage('Clone Repository') {
	  steps {
		checkout scm
	  }
	}
	stage('Build Docker Images') {
	  steps {
	        container('docker') {	
		  sh "docker build -t ${imageTag} ."
		}
	  }
	}
	stage('Push Images') {
	  steps {
		container('docker') {
		  sh "docker push ${imageTag}"
		}
	  }
	}
	stage('Deploy Canary') {
	  // Canary branch
	  when { branch 'canary' }
	  steps {
		container('kubectl') {
		  // Change deployed image in canary to the one we just built
		  sh("sed -i.bak 's#docker.adzkia.web.id/ramadoni/nginx-hello:latest#${imageTag}#' ./k8s/canary/*.yaml")
		  sh("kubectl --namespace=production apply -f k8s/services/")
		  sh("kubectl --namespace=production apply -f k8s/canary/")
		  sh("echo http://`kubectl --namespace=production get service/${feSvcName} -o jsonpath='{.status.loadBalancer.ingress[0].ip}'` > ${feSvcName}")
		} 
	  }
	}
	stage('Deploy Production') {
	  // Production branch
	  when { branch 'master' }
	  steps{
		container('kubectl') {
		// Change deployed image in canary to the one we just built
		  sh("sed -i.bak 's#docker.adzkia.web.id/ramadoni/nginx-hello:latest#${imageTag}#' ./k8s/production/*.yaml")
		  sh("kubectl --namespace=production apply -f k8s/services/")
		  sh("kubectl --namespace=production apply -f k8s/production/")
		  sh("echo http://`kubectl --namespace=production get service/${feSvcName} -o jsonpath='{.status.loadBalancer.ingress[0].ip}'` > ${feSvcName}")
		}
	  }
	}
	stage('Deploy Dev') {
	  // Developer Branches
	  when { 
		not { branch 'master' } 
		not { branch 'canary' }
	  } 
	  steps {
		container('kubectl') {
		  // Create namespace if it doesn't exist
		  sh("kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}")
		  // Don't use public load balancing for development branches
		  sh("sed -i.bak 's#LoadBalancer#ClusterIP#' ./k8s/services/frontend.yaml")
		  sh("sed -i.bak 's#docker.adzkia.web.id/ramadoni/nginx-hello:latest#${imageTag}#' ./k8s/dev/*.yaml")
		  sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/services/")
		  sh("kubectl --namespace=${env.BRANCH_NAME} apply -f k8s/dev/")
		  echo 'To access your environment run `kubectl proxy`'
		  echo "Then access your service via http://localhost:8001/api/v1/proxy/namespaces/${env.BRANCH_NAME}/services/${feSvcName}:80/"
		}
	  }     
	}
	}
}
