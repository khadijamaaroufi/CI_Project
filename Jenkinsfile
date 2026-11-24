pipeline {
    agent any
    stages {
        stage('Nettoyage Workspace') {
            steps {
                deleteDir()
            }
        }
        stage('Clonage') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/khadijamaaroufi/CI_Project.git'
            }
        }
        stage('Vérification Python') {
            steps {
                bat 'python --version'
                bat 'python -m pip --version'
            }
        }
        stage('Installation des dépendances') {
            steps {
                bat 'python -m pip install -r requirements.txt'
            }
        }
        stage('Analyse qualité du code (Pylint)') {
            steps {
                bat 'python -m pylint calculator.py || exit /b 0'
            }
        }
        stage('Tests unitaires') {
            steps {
                bat 'python -m pytest --ignore=Release'
            }
        }
        stage('Création de l artefact') {
            steps {
                bat '''
                if exist Release rmdir /S /Q Release
                if exist Release.zip del Release.zip
                mkdir Release
                copy calculator.py Release
                copy test_calculator.py Release
                copy requirements.txt Release
                powershell Compress-Archive -Path Release\\* -DestinationPath Release.zip -Force
                '''
            }
        }
        stage('Publication de l artefact') {
            steps {
                bat '''
                curl -v ^
                -u admin:98349c12-66c6-40b0-98a6-46b889c5eaf8 ^
                --upload-file Release.zip ^
                http://localhost:8081/repository/python-release/Release.zip
                '''
            }
        }
    }
    post {
        always {
            echo '=============================='
            echo 'Pipeline terminé'
            echo '=============================='
        }
        success {
            echo 'BUILD SUCCESS'
        }
        failure {
            echo 'BUILD FAILED'
        }
    }
}