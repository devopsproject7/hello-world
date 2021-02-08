currentBuild.displayName = "Devops learning # "+currentBuild.number

pipeline{
   agent any

     tools {
         git  'git2'      //  /bin/git  in Global tool   config
     // maven 'maven3'    //maven added to PATH
    }

    environment{                    //tool: pre-defined tool installation
       def mvnHOME = tool name: 'maven3', type: 'maven'  
        DOCKER_TAG = getDockerTag()
    }

    triggers {
    pollSCM '*/1 * * * *'
   }



  stages{


    stage("SCM checkout "){
        steps{
            echo "Hello world!!"                    //---> git
            git branch: 'master', url: 'https://github.com/devopsproject7/hello-world.git'
        }
    }



    stage("maven build"){
        steps{
            script{
            sh 'echo $PATH'            
            def mvnHome = tool name: 'maven3', type: 'maven'    //--->tool: pre-defined tool installation
            sh "${mvnHome}/bin/mvn package"
            } 

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
                // dhttps://hooks.slack.com/services/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ef qg = waitForQualityGate()
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

https://hooks.slack.com/services/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
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
            steps{v1
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
            //      https://hooks.slack.com/services/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          sh "ssh ec2-user@52.66.70.61 kubectl create -f ."
             //           }
              //      }
              //  }
            }
        }


   stage("Email notification"){                 //--->mail
       steps{
             echo "Email notification!!"
    //        mail bcc: '', body: 'Hello', cc: '',
    //        from: '', replyTo: '',
    //        subject: 'Hello World!!!', to: 'devopsvid07@gmail.com'

       }

    }
   stage('Slack notification'){              //--->SlackSend
       steps{                                //https://hooks.slack.com/services/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
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



