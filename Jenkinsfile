pipeline { 
    agent none 
    stages  { 
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
		/* --------------- NUEVO STAGE PARA SEMGREP ------------------- */
	stage('SAST') {
               agent any
				steps {
					script {
						sh '''
							echo "[INFO] Ejecutando Semgrep SAST dentro de Jenkins..."
							mkdir -p reports

							# Ejecutar el escaneo con Semgrep
							semgrep scan --config auto --json --output reports/semgrep-report.json || true

							echo "[INFO] Escaneo completado. Analizando severidades..."

							# Contar vulnerabilidades High / Critical
							if command -v jq >/dev/null 2>&1; then
								HIGH=$(jq '[.results[] | select(.extra.severity=="HIGH" or .extra.severity=="CRITICAL")] | length' reports/semgrep-report.json)
							else
								HIGH=$(python3 - <<'PY'
							import json
							r=json.load(open("reports/semgrep-report.json"))
							print(sum(1 for x in r.get("results",[]) if x.get("extra",{}).get("severity","").upper() in ("HIGH","CRITICAL")))
							PY
							)
							fi

							echo "[INFO] Vulnerabilidades High/Critical encontradas: $HIGH"

							if [ "$HIGH" -gt 0 ]; then
								echo "[ERROR] Se encontraron vulnerabilidades High/Critical."
								exit 1
							fi
						'''
					}
				}
            post {
                always {
                    archiveArtifacts artifacts: 'reports/semgrep-report.json', fingerprint: true
                }
            }
        }
        /* ------------------------------------------------------------ */
	}	 
}
