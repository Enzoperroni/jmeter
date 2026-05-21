pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Rodar teste JMeter') {
            steps {
                sh '''
                docker run --rm \
                  -v "$WORKSPACE/testes:/tests" \
                  justb4/jmeter \
                  -n -t /tests/teste_petstore.jmx \
                  -l /tests/resultados.jtl \
                  -e -o /tests/relatorio
                '''
            }
        }

        stage('Arquivar resultados') {
            steps {
                archiveArtifacts artifacts: 'testes/resultados.jtl, testes/relatorio/**', fingerprint: true
            }
        }
    }
}