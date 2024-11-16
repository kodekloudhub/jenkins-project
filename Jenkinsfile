pipeline 
{
	agent any

	environment {
		IMAGE_NAME='sunlnx/jenkins-flask-app'
		IMAGE_TAG="${IMAGE_NAME}:${env.GIT_COMMIT}"
	}
    
	stages 
    {
        
		stage('Setup') 
        {
			steps {
				sh "pip install -r requirements.txt"
			}
        }

		stage('Test')
        {
			steps {
				sh "pytest"
			}
		}

	    		stage('Build Docker Image') 
        {
			steps {
				sh 'docker build -t ${IMAGE_TAG} .'
				echo "Docker image build successfully"
				sh 'docker images ls'
			}
		}

		stage('Login to Dockerhub') 
        {
			steps {
				withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {

				sh 'echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin' 
				echo 'login successful'
				}

			}
		}

		stage('Push Docker image') 
        {
			steps {
				sh "docker push ${IMAGE_TAG}"
				echo "Docker image pushed successfully."
			}
		}

	}

}
