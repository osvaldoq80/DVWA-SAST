pipeline {
    agent any
    stages {
        stage('SAST-Semgrep') {
            agent {
                docker {
                    image 'returntocorp/semgrep:latest'  // imagen oficial con semgrep y git ya instalados
                }
            }
            steps {
                script {
                    sh '''
                        echo "[INFO] Iniciando escaneo SAST con Semgrep..."
                        semgrep scan --json --output semgrep.json --disable-version-check .
                    '''
                }
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

    post {
        always {
            archiveArtifacts artifacts: 'semgrep.json', fingerprint: true
        }
    }
}
