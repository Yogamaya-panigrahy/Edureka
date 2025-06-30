pipeline {
    		agent any
    		environment 
		{
        	IMAGE_NAME = 'abctech-app'
        	DOCKERHUB_IMAGE = " yogamaya21/abctech-app:latest"
		DOCKER_HUB_CREDENTIALS_ID = 'docker-hub-creds' // Jenkins credential ID
    		}

    	stages {

	stage('Code Checkout')
	{
			steps
			{
				git url: 'https://github.com/Yogamaya-panigrahy/Edureka.git' , branch: 'main'
			}
	}
        stage('Build WAR') 
	{
            steps {
                
                    sh 'mvn clean package'
                
            	 }
        }
        stage('Build Docker Image') 
	{
            steps 
		{
                sh 'docker build -t $IMAGE_NAME .'
            	}
        }
        stage('Push to Docker Hub') 
	{
    	environment 
		{
        		DOCKER_HUB_REPO = 'yogamaya21/abctech-app'
        		
    		}
    		steps {
            		// Log in to Docker Hub
            		script {
    				withCredentials([usernamePassword(
        			credentialsId: "${DOCKER_HUB_CREDENTIALS_ID}",
        			usernameVariable: 'DOCKER_USER',
        			passwordVariable: 'DOCKER_PASS')]) {

        			sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
        			sh "docker tag ${IMAGE_NAME} ${DOCKER_HUB_REPO}"
        			sh "docker push ${DOCKER_HUB_REPO}"
   			 }
			}

        	     }
    	}


	stage('Run Container') 
	{
    	environment {
        DOCKER_HUB_REPO = 'yogamaya21/abctech-app'
    	}
    	steps {
        	script {
            // Stop and remove existing container if it's running
            sh """
                docker rm -f abctech-container || true
                docker pull ${DOCKER_HUB_REPO}
                docker run -d -p 8080:8080 --name abctech-container ${DOCKER_HUB_REPO}
            """
        }
    }
}
stage('Deploy to Kubernetes') {
    environment {
        KUBE_CONFIG_CREDENTIALS_ID = 'kubeconfig-cred-id' // Replace with your actual Jenkins credential ID
        K8S_NAMESPACE = 'default' // Or your desired namespace
    }
    steps {
        withCredentials([file(credentialsId: "${KUBE_CONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
            script {
                // Apply Kubernetes manifest

                sh """
                    kubectl --kubeconfig=$KUBECONFIG delete deployment abctech-deployment --namespace=${K8S_NAMESPACE} --ignore-not-found
                    kubectl --kubeconfig=$KUBECONFIG apply -f k8s/deployment.yaml --namespace=${K8S_NAMESPACE}
                """
            }
        }
    }
}
}
}





