pipeline { 
    agent none 
    stages  {
 /* --------------- STAGE PARA SEMGREP ------------------- */
	stage('SAST') {
            steps {
                script {
                    sh """
                        echo "[INFO] Iniciando escaneo SAST con Semgrep..."
                        mkdir -p reports

                        docker run --rm \
                            -v "${PWD}:/src" \
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

/* ------------------------------------------------------------ */
 
        stage('Checkout') { 
		agent { 
			docker { image 'php:8.2-cli' } 
		} 
		steps { 
			sh 'php --version'
		} 
	}
	stage('Compilation') { 
		agent {
			docker { image 'php:8.2-cli' } 
		} 
		steps { 
			sh 'echo "Compilando..."' 
		} 
	} 
	stage('Build') { 
		agent { 
			docker { image 'php:8.2-cli' } 
		} 
		steps { 
			sh 'echo "docker build -t my-php-app ."' 
		} 
	} 
	stage('Deploy') { 
		agent {
			docker { image 'php:8.2-cli' } 
		} 
		steps { 
			sh 'echo "docker run my-php-app ."'
		} 
	} 
	
   }	 
}
