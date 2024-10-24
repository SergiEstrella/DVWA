pipeline {
    agent any

    environment {
        // Variables de entorno necesarias para SonarQube
        SONARQUBE_SERVER = 'sonarqube-webhook'  // Nombre del servidor SonarQube configurado en Jenkins
        SONAR_HOST_URL = 'http://10.30.212.42:9000'  // URL del servidor SonarQube
        SONAR_AUTH_TOKEN = credentials('sonarqubbe')  // Token de autenticación de SonarQube
        SONAR_SCANNER_HOME = '/opt/sonar-scanner-6.2.1.4610-linux-x64/bin'  // Ruta del sonar-scanner en tu sistema
    }

    stages {
        // 1. Etapa de Checkout para clonar el código desde el repositorio
        stage('Checkout') {
            steps {
                git url: 'https://github.com/SergiEstrella/DVWA.git', branch: 'main'
            }
        }

        // 2. Etapa de Compilación/Build
        stage('Build') {
            steps {
                echo 'Building the project...'
                // Si se requiere un script de compilación, se puede agregar aquí
                // Por ejemplo, si se usa Maven o Gradle:
                // sh 'mvn clean install'
                // O si es un proyecto Node.js:
                // sh 'npm install'
            }
        }

        // 3. Etapa de Análisis de SonarQube
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-webhook') {  // Usamos el nombre correcto del servidor
                    // Ejecutar el scanner de SonarQube con la ruta especificada
                    sh """
                        ${SONAR_SCANNER_HOME}/opt/sonar-scanner-6.2.1.4610-linux-x64/bin \
                        -Dsonar.projectKey=Pipeline_Sonarqube
                        -Dsonar.projectName=Pipeline_Sonarqube
                        -Dsonar.sources=.
                        -Dsonar.host.url=${SONAR_HOST_URL}
                        -Dsonar.login=${SONAR_AUTH_TOKEN}
                    """
                }
            }
        }

        // 4. Etapa de Pruebas (Unit Tests)
        stage('Unit Tests') {
            steps {
                echo 'Running unit tests...'
                // Ejecuta pruebas unitarias, por ejemplo:
                // Para un proyecto Node.js: sh 'npm test'
                // O cualquier otra herramienta de pruebas que estés utilizando
            }
        }

        // 5. Etapa de Espera para el Quality Gate de SonarQube
        stage('Quality Gate') {
            steps {
                script {
                    // Espera hasta que el análisis sea procesado por SonarQube y devuelve el resultado del Quality Gate
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
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
                branch 'main'  // Solo se ejecuta el despliegue si estamos en la rama main
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



