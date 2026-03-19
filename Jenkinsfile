pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:8.0'
            args '-u root' 
        }
    }

    environment {
        // O aluno deve colocar o IP da máquina dele da AWS aqui
        AWS_IP = '44.222.255.54' 
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Baixando o código do repositório...'
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                echo 'Restaurando pacotes e dependências do NuGet...'
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                echo 'Compilando a aplicação .NET...'
                sh 'dotnet build --configuration Release --no-restore'
            }
        }

        stage('Test') {
            steps {
                echo 'Executando a suíte de testes unitários...'
                sh 'dotnet test --configuration Release --no-build --verbosity normal'
            }
        }

        stage('Publish') {
            steps {
                echo 'Empacotando a API para deploy...'
                sh 'dotnet publish ExemploDevOps.Api/ExemploDevOps.Api.csproj -c Release -o ./publish-output'
            }
        }

        stage('Deploy to AWS') {
            steps {
                echo 'Iniciando o deploy direto para a AWS...'
                
                // sh 'apt-get update && apt-get install -y openssh-client'
                // sh 'mkdir -p ~/.ssh && echo "StrictHostKeyChecking no" >> ~/.ssh/config'

                withCredentials([sshUserPrivateKey(credentialsId: 'aws-aula02', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    
                    echo 'Transferindo arquivos via SCP...'
                    sh 'scp -i $SSH_KEY -r ./publish-output/* ${SSH_USER}@${AWS_IP}:/var/www/ExemploDevOps/'

                    echo 'Reiniciando o serviço da API...'
                    sh 'ssh -i $SSH_KEY ${SSH_USER}@${AWS_IP} "sudo systemctl restart webapi"'
                }
            }
        }

    }
}