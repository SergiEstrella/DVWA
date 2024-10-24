pipeline {
    agent any

    environment {
        // Nombre del servidor SonarQube configurado en Jenkins
        SONARQUBE_SERVER = 'sonarqube-webhook'
        // Agregar sonar-scanner al PATH
        PATH = "/opt/sonar-scanner-6.2.1.4610-linux-x64/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Clonar el código fuente desde el repositorio
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                // Configurar el entorno de SonarQube
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    // Ejecutar el análisis con SonarScanner
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=Pipeline_Sonarqube \
                        -Dsonar.sources=vulnerabilities \
                        -Dsonar.php.version=8.0
                    '''
                }
            }
        }
        stage('Quality Gate') {
            steps {
                // Esperar el resultado del Quality Gate
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
