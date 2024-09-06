pipeline{
    agent any

    environment {
        PATH = "$HOME/bin:$PATH"
    }


    stages {
  
       stage('Install AWS CLI') {
        steps{
            script {
                sh """
                if which aws &> /dev/null

                then

                    echo "AWS CLI is already installed"
                    echo $PATH

                else
                    echo "AWS CLI not found, Installing..."
                    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                    unzip awscliv2.zip
                    ./aws/install --bin-dir ~/bin --install-dir ~/aws-cli --update
                    export PATH=~/bin:$PATH
                fi

                """
            }
        }
       }


        stage('Clone Repository'){
            steps{
                git branch: 'master', url: 'https://github.com/SiddhantGahlot5599/Project-1.git'
            }
        }

        stage('Build'){
            steps {
                echo 'Building the web applicatoin...'
            }
        }

        stage('Deploy to AWS CodeDeploy') {
            steps{
                script {
                    withAWS(region: 'ap-south-1', credentials:'Web-App') {
                       // get the latest commit ID from the repository 
                       def commitId = sh(script: "git rev-parse HEAD", returnStdout: true).trim()
                       echo "Commit ID: ${commitId}"
                       env.COMMIT_ID = commitId

                       sh """
                       
                       aws deploy create-deployment \
                       --application-name My-webApp \
                       --deployment-group-name My-webapp-dg \
                       --github-location repository=SiddhantGahlot5599/Project-1,commitId=${env.COMMIT_ID} \
                       --region ap-south-1
                       """

                    }
                }
            }
        }

    }

    post {
        always {
            echo 'Pipeline execution complete!'
        }
    }
}