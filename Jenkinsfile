pipeline {

   agent any
   stages {
    stage('Repository Build') {

        steps {

            // Get service code from a GitHub repository

            git credentialsId: '38473-094R9R-CREDENTIAL-387934', url: "git@github.com:Happytocodehere/bootstrap.git", branch: 'beta'
            sh "npm install"
	        sh "ng build --output-hashing=all"

        }
        post {

          failure {
                /* Use slackNotifier.groovy from shared library and provide current build result as parameter */
            slackSend channel: 'devops-team',
                          message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"

            }
                }
    }

	stage('Nexus Push'){
        steps {
            /* To be added */
			sh "echo 'Nexus push'"
        }
                post {
            failure {
                 /* Use slackNotifier.groovy from shared library and provide current build result as parameter */
             slackSend channel: 'devops-team',
                          message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            }
        }
    }

    stage('Beta Deployment'){
      steps {
            sh "sed -i 's/localhost/sampleurl.com/g' /var/lib/jenkins/workspace/bootstrap/dist/node/assets/config/appConfig.json"

            sh "ansible-playbook deploy.yaml --tags beta"
            sh "echo 'Beta Server deployment'"
            }
            post {
              failure {
                 /* Use slackNotifier.groovy from shared library and provide current build result as parameter */
             slackSend channel: 'devops-team',
                          message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"

            }
        }
      }


	stage('Promote to RELEASE') {
        steps {
            sh "rm -rf release"
            dir ('release') {
                sh "git clone git@github.com:Happytocodehere/bootstrap.git"
                sh "cd ${env.JOB_BASE_NAME}"
                sh "git checkout beta"
                sh "git checkout release"
                sh "git merge beta"
                sh "git push origin release"
           }

        }
                post {

            failure {
                 /* Use slackNotifier.groovy from shared library and provide current build result as parameter */
             slackSend channel: 'devops-team',
                          message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"

            }
        }
	}
	}
	post {

        always {
                 /* Use slackNotifier.groovy from shared library and provide current build result as parameter */
             slackSend channel: 'devops-team',
                          message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"



        }

        success {
             emailext (
                to: "yourmailid@mail.com ; yourbossmailid@mail.com ; admin@mail.com",
                subject: "Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} : ${currentBuild.currentResult} ",
                body: "Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} : ${currentBuild.currentResult} \n More info at: ${env.BUILD_URL} \n Please contact johndoe@mail.com for assistance."
              )
        }

        failure {
                         emailext (
                to: "yourteam@mail.com",
                subject: "Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} : ${currentBuild.currentResult} ",
                body: "Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} : ${currentBuild.currentResult} \n More info at: ${env.BUILD_URL} \n Please contact johndoe@mail.com for assistance."
                )
        }

	}
	}
