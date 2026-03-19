pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:8.0'
            args '-u root' 
        }
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

    }
}