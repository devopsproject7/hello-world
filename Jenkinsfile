currentBuild.displayName = "Devops learning # "+currentBuild.number

pipeline{
   agent any
     tools {
         git  'git2'
     // maven 'maven3'    //maven added to PATH
    }

    environment{
       def mvnHOME = tool name: 'maven3', type: 'maven'  
        DOCKER_TAG = getDockerTag()
    }

    triggers {
    pollSCM '*/1 * * * *'
   }

  stages{
    stage("SCM checkout "){
        steps{
            echo "Hello world!!"
            git branch: 'master', url: 'https://github.com/devopsproject7/hello-world.git'
        }
    }
    stage("maven build"){
        steps{
            sh 'echo $PATH'
            sh '${mvnHOME}/bin/mvn  package'

        }
    }
    stage('SonarQube Analysis') {
        steps{
            echo "SonarQube Analysis!!"
           // withSonarQubeEnv('sonar6') { // You can override the credential to be used
           // sh '${mvnHOME}/bin/mvn  sonar:sonar'
           // }
          }                                            
        }


    stage("Quality Gate Status Check"){
        steps{
         script{
          timeout(time: 1, unit: 'HOURS') {
                // def qg = waitForQualityGate()
                //  if (qg.status != 'OK') {
               //   error "Pipeline aborted due to quality gate failure: ${qg.status}"
                //  slackSend baseUrl: 'https://hooks.slack.com/services/', 
                //  channel: '#devopsprj_notify', color: 'good',
                //  message: 'Hello from Slack', tokenCredentialId: 'SlackDemo'
              //    }
          }
        }
        }
    }


    stage('Build Docker Image'){
            steps{
                script{
                    echo "hello tag is: ${DOCKER_TAG} "
                    sh "docker build . -t devopsharish/nodeapp:${DOCKER_TAG}"
                    sh "whoami"
                    sh "docker images"
                }
                
            }

        }
    stage("Dockerhub push"){
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubPwd')]) {

                sh "docker login -u devopsharish -p  ${dockerhubPwd}"
                sh "docker push devopsharish/nodeapp:${DOCKER_TAG}"
            }
        }
    }
 

            stage('Push  to DockerHub'){
            steps{
                 withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhubPwd')]) {
                    sh "docker login -u devopsharish -p  ${dockerhubPwd}"
                    script{
                        def tag = latestCommitHash()
                        sh """
                            docker push devopsharish/nodeapp:${tag}
                            chmod +x changeTag.sh
                            ./changeTag.sh ${tag}
                        """
                    }
                }
            }
        }
        stage('Deploy - Kubernetes'){
            steps{
                echo "Deploy to k8s"
            //    sshagent(['kops-k8s']) {
            //       sh """ 
            //           scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@52.66.70.61:/home/ec2-user/
            //           
             //       """
            //        script{
             //           try{
            //                sh "ssh ec2-user@52.66.70.61 kubectl apply -f ."
             //           }catch(error){
            //                sh "ssh ec2-user@52.66.70.61 kubectl create -f ."
             //           }
              //      }
              //  }
            }
        }


   stage("Email notification"){
       steps{
             echo "Email notification!!"
    //        mail bcc: '', body: 'Hello', cc: '',
    //        from: '', replyTo: '',
    //        subject: 'Hello World!!!', to: 'devopsvid07@gmail.com'

       }

    }
   stage('Slack notification'){
       steps{
            echo "slack notification!!"
      //       slackSend baseUrl: 'https://hooks.slack.com/services/', 
      //       channel: '#devopsprj_notify', color: 'good',
      //      message: 'Hello from Slack', tokenCredentialId: 'SlackDemo'

       }
   }




   }
}



def getDockerTag(){
    def commit =  sh script: 'git rev-parse --short HEAD', returnStdout: true
    return commit
}

def latestCommitHash(){
    def commit =  sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commit
}



