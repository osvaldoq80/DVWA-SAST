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
				agent {
					docker {
						image 'semgrep/semgrep:latest'
						args '-v $WORKSPACE:/src -w /src'
					}
				}
				steps {
					script {
						sh '''
							echo "[INFO] Iniciando escaneo SAST con Semgrep..."
							mkdir -p reports

							semgrep scan --config auto --json --output reports/semgrep-report.json || true

							echo "[INFO] Escaneo finalizado. Revisando severidades..."

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

