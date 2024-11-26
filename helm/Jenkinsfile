pipeline {
    agent {
        label 'AGENT-1'
    }

    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'prod'], description: 'Select the environment to deploy')
        string(name: 'VERSION', defaultValue: '', description: 'Specify the application version ')
    }
    environment { 
        appVersion = ''
        account_id = '315069654700'
        region = 'us-east-1'
        project = 'expense'
        component = 'backend'
        environment = ''
    }
    
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    // Set account ID based on selected environment
                    environment = params.ENVIRONMENT
                    if (params.ENVIRONMENT == 'dev') {
                        account_id = '315069654700'
                        
                    } else if (params.ENVIRONMENT == 'prod') {
                        account_id = '315069654700' // Replace with your prod account ID
                    }

                    if (params.VERSION) {
                        appVersion = params.VERSION
                        echo "Using user-specified version: $appVersion"
                    }

                    environment = params.ENVIRONMENT
                    echo "Selected environment: ${environment}"
                    echo "Account ID: ${account_id}"
                }
            }
        }
        stage('Deploy'){
            steps{
                withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                    script{               
                        echo "${component} not installed yet, first time installation"
                        sh"""
                            aws eks update-kubeconfig --region ${region} --name ${project}-${environment}
                            cd helm
                            sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${environment}.yaml
                            helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will run when pipeline is success'
        }
        failure { 
            echo 'I will run when pipeline is failure'
        }
    }
}