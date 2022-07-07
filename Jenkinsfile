pipeline{
     environment{
	   IMAGE_NAME = "alpinehelloworld"
	   IMAGE_TAG = "latest"
	   STAGING = "hippo_production"
    }
	agent none
	stages {
	    stage('build image'){
		    agent any
			steps{
			    script{
				  sh 'docker build -t hippo2451/$IMAGE_NAME:$IMAGE_TAG .'
				}
            }		
		}
		stage('Run container based on builded image'){
		    agent any
			steps{
			    script{
				  sh '''
				  docker run --name $IMAGE_NAME -d -p 80:5000 -e PORT=5000 hippo2451/$IMAGE_NAME:$IMAGE_TAG
				  sleep 5
				  '''
				  
				}
            }		
		}
		stage('test image'){
		    agent any
			steps{
			    script{
				  sh '''
				     curl http://localhost | grep -q "Hello world!"
				  '''
				  
				}
            }		
		}
		stage('clean container'){
		    agent any
			steps{
			    script{
				  sh '''
				     docker stop $IMAGE_NAME
					 docker rm $IMAGE_NAME
				  '''
				  
				}
            }		
		}
		stage('push image in staging and deploy it'){
		  when {
		         expression { GIT_BRANCH == 'origin/master' }
		    }
		  agent any
		  environment{
		      HEROKU_API_KEY = credentials('heroku_api_key')
		   }
		  steps{
			    script{
				  sh '''
				     heroku container:login
					 heroku create $STAGING || echo "project already exist"
					 heroku container:push -a $STAGING web
					 heroku container:release -a $STAGING web
				  '''
				  
				}
            }		
		
	    }
		stage('push image in production and deploy it'){
		  when {
		         expression { GIT_BRANCH == 'origin/master' }
		    }
		  agent any
		  environment{
		      HEROKU_API_KEY = credentials('heroku_api_key')
		   }
		  steps{
			    script{
				  sh '''
				     heroku container:login
					 heroku create $PRODUCTION || echo "project already exist"
					 heroku container:push -a $PRODUCTION web
					 heroku container:release -a $PRODUCTION web
				  '''
				  
				}
            }		
		
	    }
	}
}