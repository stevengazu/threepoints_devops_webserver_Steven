pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo 'Descargando código del repositorio Git...'
                checkout scm // Ajusta esto según el plugin de Git en Jenkins y tu configuración
            }
        }
        stage('Pruebas de SAST') {
            steps {
                echo 'Ejecución de pruebas de SAST' // Aquí se realiza el mock
            }
        }
        stage('Build') {
            steps {
                script {
                    def isAWS = false // Cambia a "true" si estás usando la imagen AWS
                    if (isAWS) {
                        sh 'sudo chmod g+x src/index.js && docker build -t dvwa .'
                    } else {
                        sh 'docker build -t devops_ws .'
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline terminado.'
        }
    }
}
