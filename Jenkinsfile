pipeline {
    
    agent {
        label "slave1"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/0ayush98/javawebapp.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  ayush98/javawebapp:v${BUILD_ID}  ."
                //sh 'whoami'
            }
            
        }
        
        
        stage('Push Image to Docker HUB') {
            steps {
                    withCredentials([string(credentialsId: 'DOCKER_PWD', variable: 'DOCKER_CRED')])  {
    
              sh "sudo docker login -u ayush98 -p $DOCKER_CRED"
        }
 
               

               
               sh "sudo docker push ayush98/javawebapp:v${BUILD_ID}"
            }
            
        }
        
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run  -d  -p  8080:8080 --name myjavaapp   ayush98/javawebapp:v${BUILD_ID}"
                //sh 'whoami'
            }
            
        }
        
        
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QAT_ENVV']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.233.118.132 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@13.233.118.132 sudo docker run  -d  -p  8008:8080 --name myjavaapp   ayush98/javawebapp:v${BUILD_ID}"
                }

            }
            
        }
        



        
         stage('QAT Test') {
            steps {
                
               // sh 'curl --silent http://13.233.118.132:8080/java-web-app/ |  grep India'
                
                retry(10) {
                    sh 'curl --silent http://13.233.118.132:8008/java-web-app/ |  grep India'
                }
            
               
            }
        }
          
        
         
         
        stage('approved') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
        
        
         
        
        stage('Deploy webAPP in Prod Env') {
            steps {
               
               sshagent(['QAT_ENVV']) {
    
                     sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.127.46.180 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@13.127.46.180 sudo docker run  -d  -p  8005:8080 --name myjavaapp   ayush98/javawebapp:v${BUILD_ID}"
                    
                }

            }
            
        } 
        
    
        
    }
    
  
   

    
    
}