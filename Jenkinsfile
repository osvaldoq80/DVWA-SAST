pipeline { 
    agent none 
    stages  {
 /* --------------- STAGE PARA SEMGREP ------------------- */
	stage('SAST') {
		agent any
			steps {
				script {
					sh """
						echo "[INFO] Iniciando escaneo SAST con Semgrep..."
						mkdir -p reports

						# Se ejecuta Semgrep en un contenedor aparte montando el workspace
						docker run --rm \
							-v "${WORKSPACE}:/src" \
							-w /src \
							semgrep/semgrep:latest \
							semgrep scan --config p/owasp-top-ten \
							--json --output reports/semgrep-report.json \
							--disable-version-check

						echo "[INFO] Escaneo finalizado. Revisando severidades..."
					"""
				}
			}
			post {
				always {
					archiveArtifacts artifacts: 'reports/semgrep-report.json', fingerprint: true
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

