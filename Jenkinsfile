pipeline {
    agent any

    environment {
        // Variables de entorno necesarias para SonarQube
        SONARQUBE_SERVER = 'sonarqube-webhook'  // Nombre del servidor SonarQube configurado en Jenkins
        SONAR_HOST_URL = 'http://10.30.212.42:9000'  // URL del servidor SonarQube
        PATH = "/opt/sonar-scanner-6.2.1.4610-linux-x64/bin:${env.PATH}"  // Ruta del sonar-scanner en tu sistema
    }

    stages {
        // 1. Etapa de Checkout para clonar el código desde el repositorio
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // 3. Etapa de Análisis de SonarQube
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {  // Usamos el nombre correcto del servidor
                    // Ejecutar el scanner de SonarQube
                    sh """
                        sonar-scanner \
                        -Dsonar.projectKey=Pipeline_Sonarqube \
                        -Dsonar.sources=vulnerabilities \
                        -Dsonar.php.version=8.0
                    """
                }
            }
        }

        // 5. Etapa de Espera para el Quality Gate de SonarQube
        stage('Quality Gate') {
            steps {
                script {
                    // Espera hasta que el análisis sea procesado por SonarQube y devuelve el resultado del Quality Gate
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'EXECUTION SUCCESS') {
                        error "Quality Gate failed: ${qualityGate.status}"
                    } else {
                        echo "Quality Gate passed: ${qualityGate.status}"
                    }
                }
            }
        }

        // 6. Etapa de Despliegue (opcional)
        stage('Deploy') {
            when {
                branch 'master'  // Solo se ejecuta el despliegue si estamos en la rama main
            }
            steps {
                echo 'Deploying to the server...'
                // Aquí se añadiría el código para desplegar la aplicación en el servidor
                // Ejemplo: sh './deploy.sh'
            }
        }
    }

    post {
        always {
            // Limpiar el workspace después de la ejecución, si es necesario
            cleanWs()
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}

