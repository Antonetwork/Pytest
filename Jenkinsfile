pipeline {
    agent { label 'Slave1' }  // Utiliser le slave Windows avec ce label

    environment {
        PYTHON_ENV = 'venv'  // Nom de l'environnement virtuel Python
        TEST_RESULTS_DIR = 'C:\\Jenkins\\test_results'  // Répertoire sur le slave Windows pour les résultats des tests
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm  // Récupère le code source depuis le dépôt Git
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    // Créer et activer un environnement virtuel Python
                    bat 'python -m venv %PYTHON_ENV%'
                    bat '%PYTHON_ENV%\\Scripts\\pip install -r requirements.txt'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Exécuter pytest et générer un rapport XML dans le répertoire de résultats
                    bat '%PYTHON_ENV%\\Scripts\\pytest --maxfail=1 --disable-warnings -q --junitxml=%TEST_RESULTS_DIR%\\test-results.xml'
                }
            }
        }

        stage('Archive Test Results') {
            steps {
                // Stocker les résultats dans Jenkins, mais on les a déjà dans le répertoire local
                archiveArtifacts artifacts: '%TEST_RESULTS_DIR%\\test-results.xml', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo "Test results are stored at %TEST_RESULTS_DIR%\\test-results.xml"
        }
    }
}
