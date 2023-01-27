pipeline {
    agent any
  
    environment {
	    DOCKERHUB_CREDENTIALS=credentials('dockerhub')
      AWS_DEFAULT_REGION="us-east-1"
    	THE_BUTLER_SAYS_SO=credentials('aws-cred2')
    }

    stages {
      stage('Checkout Code') {
            steps {
                // Checkout the code from the repository
                git url: 'https://github.com/adeoyedewale/DevOps-Challenge.git'
            }
        }
	    
      stage('Build Backend') {
                steps {
        sh 'cd backend && npm ci && npm install'
                    sh 'cd ..'
                }
            }

      stage('Build Frontend') {
                steps {
        sh 'cd frontend && npm ci && npm install && npm run build'
                    sh 'cd ..'
                }
            }

      stage('Create Docker Image') {
          steps {
        sh 'docker build -t eruobodo/devops-challenge-image:$BUILD_NUMBER .'
          }
      }
      stage('Push') {
          steps {
            withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
              sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
              sh 'docker push eruobodo/devops-challenge-image:$BUILD_NUMBER'
            }
          }
      }
      }

      stage ('Build and Publish to ECR') {
            steps {
              sh '''
                  aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c6p1p1z3
                  docker tag eruobodo/devops-challenge-image:$BUILD_NUMBER public.ecr.aws/c6p1p1z3/devops-code-challenge:$BUILD_NUMBER
                  docker push public.ecr.aws/c6p1p1z3/devops-code-challenge:$BUILD_NUMBER
              '''
            }
      } 
   }
}
