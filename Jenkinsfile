pipeline {
    agent any
    environment {
        REPORT_EMAIL = 'antas2076@gmail.com'
    }
    stages {
        stage('Clone du code') {
            steps {
                checkout scm
            }
        }
        stage('Shift Left Scan - DevSecOps') {
            steps {
                bat '''
                    if not exist reports mkdir reports
                    docker run --rm -e "WORKSPACE=%WORKSPACE%" -v "%WORKSPACE%:/app" shiftleft/scan scan --build || exit 0
                '''
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
            emailext(
                to: "${REPORT_EMAIL}",
                subject: "ShiftLeft Scan | ${currentBuild.result} | ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <html><body style="font-family: Arial;">
                <h2 style="color:#c0392b;">Rapport DevSecOps - ShiftLeft Scan</h2>
                <table border="1" cellpadding="10" style="border-collapse:collapse;">
                  <tr><td><b>Projet</b></td><td>kubernetes-projects (04-wordpress-deployment)</td></tr>
                  <tr><td><b>Build</b></td><td>#${env.BUILD_NUMBER}</td></tr>
                  <tr><td><b>Statut</b></td><td>${currentBuild.result}</td></tr>
                  <tr><td><b>Duree</b></td><td>${currentBuild.durationString}</td></tr>
                  <tr><td><b>Outil</b></td><td>ShiftLeft Scan (multi-scanner: Semgrep, Checkov, npm-audit...)</td></tr>
                </table>
                <p>Rapports dans reports/ en piece jointe</p>
                <a href="${env.BUILD_URL}">Voir le Build Jenkins</a>
                </body></html>
                """,
                mimeType: 'text/html',
                attachmentsPattern: 'reports/*.json'
            )
        }
        success { echo 'Pipeline termine !' }
        failure { echo 'Pipeline echoue !' }
    }
}