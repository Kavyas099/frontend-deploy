pipeline {
    agent { label 'kavya' }
    environment { 
        project = 'expense'
        component = 'frontend' 
        deploy_to = "production"
        region = "us-east-1"
        appVersion = ''
        environment = ''
    }
    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }
    parameters{
        string(name: 'version',  description: 'Enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }
    stages {

        stage('Setup Environment'){
            steps{
                script{
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }
        
        stage('Deploy') {
    steps {
        script{
            withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                sh '''
                echo "===== DEBUG START ====="

                echo "Current directory:"
                pwd

                echo "Workspace files:"
                ls

                echo "Detailed listing:"
                ls -la

                echo "Trying to locate helm chart"
                find . -name "Chart.yaml"

                echo "Updating kubeconfig"
                aws eks update-kubeconfig --region $REGION --name expense-dev

                kubectl get nodes

                echo "Moving to helm directory"
                cd helm

                pwd
                ls -la

                sed -i "s/IMAGE_VERSION/${version}/g" values-${deploy_to}.yaml

                helm upgrade --install $COMPONENT -n $PROJECT -f values-${deploy_to}.yaml .

                echo "===== DEBUG END ====="
                '''
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
        failure { 
            echo 'I will run when pipeline is failed'
        }
        success { 
            echo 'I will run when pipeline is success'
        }
    }
}