pipeline {
    agent any
    parameters {
        credentials(name: 'LoginSemana4', description: 'Credenciales de usuario y contraseña')
    }
    environment {
        BRANCH_NAME = '' // Inicialización para la variable de entorno
    }
    stages {
        stage('Preparar directorio de trabajo') {
            steps {
                script {
                    // Limpia el directorio antes de clonar
                    sh '''
                    if [ -d "threepoints_devops_webserver_Steven" ]; then
                        echo "Eliminando directorio existente..."
                        rm -rf threepoints_devops_webserver_Steven
                    fi
                    '''
                    echo 'Directorio preparado para el clon.'
                }
            }
        }
        stage('Obtener nombre de la rama') {
            steps {
                script {
                    // Clonar el repositorio y asignar el nombre de la rama directamente al entorno
                    sh '''
                    git clone https://github.com/stevengazu/threepoints_devops_webserver_Steven.git
                    cd threepoints_devops_webserver_Steven
                    '''
                    env.BRANCH_NAME = sh(script: '''
                    cd threepoints_devops_webserver_Steven
                    git rev-parse --abbrev-ref HEAD
                    ''', returnStdout: true).trim()
                    echo "La rama actual es '${env.BRANCH_NAME}'"
                }
            }
        }
        stage('Validar rama') {
            steps {
                script {
                    // Validación para ejecutar solo en las ramas permitidas
                    if (!(env.BRANCH_NAME ==~ /(master|feature-.+|hotfix-.+)/)) {
                        error "El pipeline no está configurado para ejecutarse en la rama '${env.BRANCH_NAME}'."
                    }
                    echo "Ejecutando pipeline para la rama '${env.BRANCH_NAME}'."
                }
            }
        }
        stage('Pruebas de SAST') {
            steps {
                echo 'Ejecución de pruebas de SAST'
            }
        }
        stage('Crear archivo de credenciales') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'LoginSemana4',
                        usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        writeFile file: 'credentials.ini', text: """
                        [credentials]
                        user=${USERNAME}
                        password=${PASSWORD}
                        """
                        echo 'Archivo credentials.ini creado con éxito.'
                    }
                }
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    echo 'Construyendo la imagen Docker...'
                    sh '''
                    cd threepoints_devops_webserver_Steven
                    docker build -t devops_ws_eje3 .
                    '''
                    echo 'Imagen Docker construida con éxito.'
                }
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'credentials.ini', fingerprint: true
            echo 'Pipeline terminado.'
        }
    }
}