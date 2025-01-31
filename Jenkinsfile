pipeline {
    agent { label 'Slave2' }  // Exécute sur Slave1

    environment {
        PYTHON_ENV = 'venv'  // Nom de l'environnement virtuel Python
        WORKSPACE_DIR = 'C:\\Jenkins\\workspace\\Pytest'
        TEST_RESULTS_DIR = 'C:\\Jenkins\\test_results'  // Dossier pour stocker les résultats
    }

    stages {
        stage('Clean Workspace') {
            steps {
                deleteDir() // Supprime tout le contenu du workspace avant le checkout
            }
        }

        stage('Checkout SCM') {
            steps {
                script {
                    try {
                        checkout scm  // Récupère le code source depuis Git
                    } catch (Exception e) {
                        error "Échec du checkout : ${e}"
                    }
                }
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    bat 'python --version' // Vérifie si Python est installé
                    bat 'python -m venv %PYTHON_ENV%'
                    bat '%PYTHON_ENV%\\Scripts\\pip install -r requirements.txt'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    bat "mkdir %TEST_RESULTS_DIR%"  // Crée le dossier pour les résultats s'il n'existe pas
                    bat '%PYTHON_ENV%\\Scripts\\pytest --maxfail=1 --disable-warnings -q --junitxml=%TEST_RESULTS_DIR%\\test-results.xml'
                }
            }
        }

        stage('Archive Test Results') {
            steps {
                script {
                    if (fileExists('%TEST_RESULTS_DIR%\\test-results.xml')) {
                        archiveArtifacts artifacts: '%TEST_RESULTS_DIR%\\test-results.xml', allowEmptyArchive: true
                    } else {
                        error "Le fichier test-results.xml n'a pas été trouvé."
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Les résultats sont stockés sur le Slave dans %TEST_RESULTS_DIR%\\test-results.xml"
        }
    }
}
