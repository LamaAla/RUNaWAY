pipeline {

	agent any

	environment {
		DOCKERHUB_CREDENTIALS_PSW = credentials('LamaAlawwad-dockerhub-token')
        DOCKERHUB_CREDENTIALS_USR = 'lamaalawwad'
		AWS_ACCESS_KEY_ID     = credentials('LamaAlawwad-aws-secret-key-id')
  		AWS_SECRET_ACCESS_KEY = credentials('LamaAlawwad-aws-secret-access-key')
		ARTIFACT_NAME = 'Dockerrun.aws.json'
		AWS_S3_BUCKET = 'lamaalawwad-belt2-artifacts-12345'
		AWS_EB_APP_NAME = 'LamaAlawwad-EB'
        AWS_EB_ENVIRONMENT_NAME = 'Lamaalawwadeb-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_REGION = 'us-east-1'
	}

	stages {

		stage('Build') {
			steps {
				sh 'docker build -t lamaalawwad/runaway:latest .'
			}
		}

		stage('Login') {
			steps {
				sh 'docker login -u=$DOCKERHUB_CREDENTIALS_USR -p=$DOCKERHUB_CREDENTIALS_PSW'
			}
		}

		stage('Push') {
			steps {
				sh 'docker push lamaalawwad/runaway:latest'
			}
		}

        stage('Deploy') {
            steps {
                sh 'aws configure set region $AWS_REGION'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
    }
	post {
		always {
			sh 'docker logout'
		}
	}
}
