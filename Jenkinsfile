pipeline {
    agent any

    stages {
        stage('SAST - Semgrep básico') {
            steps {
                script {
                    // Guardamos el path real del workspace (más confiable que ${WORKSPACE} directo)
                    def ws = "${env.WORKSPACE}"

                    sh """
                        echo "[INFO] Iniciando escaneo SAST con Semgrep..."
                        mkdir -p reports

                        # Ejecutar Semgrep montando el workspace real
                        docker run --rm \
                            -v ${ws}:/src \
                            -w /src \
                            semgrep/semgrep:latest \
                            semgrep scan --json --output reports/semgrep-report.json --disable-version-check

                        echo "[INFO] Escaneo finalizado. Revisando severidades..."
                    """
                }
            }
            post {
                always {
                    echo "[INFO] Publicando reporte JSON de Semgrep..."
                    archiveArtifacts artifacts: 'reports/semgrep-report.json', fingerprint: true
                }
                success {
                    echo "[SUCCESS] Escaneo SAST completado correctamente."
                }
                failure {
                    echo "[ERROR] Fallo durante el escaneo SAST."
                }
            }
        }
    }
}
