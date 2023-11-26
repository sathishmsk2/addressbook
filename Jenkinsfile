pipeline {
    agent none
    stages {
        stage('Compile') {
            agent any
            steps {
                echo 'Compiling the code'
                echo "Compiling in ${params.Env}"
                sh 'mvn compile'
            }
        }
        stage('UnitTest') {
            agent any
            when{
                expression{
                    params.executeTests == true
                }
            }
            steps {
                echo 'Testing the code'
                sh 'mvn test'
            }
            post{
                always{
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Package') {
            //  agent {
            //     // Specify the label or name of the Jenkins agent (slave node) where you want to run the package stage
            //     label 'linux_slave'
            // }

            agent any
            
            input{
                message "SELECT THE ENVIRONMENT TO DEPLOY"
                ok "DEPLOY"
                parameters{
                    choice(name:'NEWAPP',choices:['ONPREM','EKS','EC2'])

            }

            }
            steps{
            script{
                sshagent(['deploy-server']) {
                    echo "Packaging the code"
                    sh "scp server-script.sh -o StrictHostKeyChecking=no ec2-user@172.31.37.128:/home/ec2-user"
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.37.128 'bash ~/server-config.sh'"  
                    // sh 'mvn package'
                    // sh "ssh "
                   
                    
                }
            }
            }
        }
    }
}

