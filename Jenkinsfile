pipeline {
	agent any
	stages {
		stage('Lint HTML') {
			steps {
				sh '''
                 cd blue/ 
                 tidy -q -e index.html
                 '''
			}
		}

		stage('Build Docker Image') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
                        cd blue/
						docker build -t ravi8636/cloudcapstone:$BUILD_ID .
					'''
				}
			}
		}

		stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
						docker push ravi8636/cloudcapstone:$BUILD_ID
					'''
				}
			}
		}

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-static') {
					sh '''
					    aws eks --region us-west-2 update-kubeconfig --name EKS-CAPSTONE
						kubectl version --short --client
						kubectl config use-context arn:aws:eks:us-west-2:556449739581:cluster/EKS-CAPSTONE
					'''
				}
			}
		}

        stage('Blue deploy') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./blue-controller.json
					'''
				}
			}
		}
        stage('Green deploy') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./green-controller.json
					'''
				}
			}
		}
        stage('Load balancer for redirection to blue') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./green-blue-service.json
					'''
				}
			}
		}
        stage('Notification with pause') {
            steps {
                input "Now we will swich traffic to green"
            }
        }
       stage('Load balancer for redirection to green') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./blue-green-service.json
					'''
				}
			}
		}
	}
}