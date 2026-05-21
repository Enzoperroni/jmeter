pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verificar arquivos') {
            steps {
                sh '''
                echo "Workspace atual:"
                pwd

                echo "Arquivos no workspace:"
                ls -la

                echo "Arquivos na pasta testes:"
                ls -la testes
                '''
            }
        }

        stage('Rodar teste JMeter') {
            steps {
                sh '''
                echo "Iniciando execução do JMeter..."

                docker run --rm \
                  --network monitoring-net \
                  -v "$WORKSPACE/testes:/tests" \
                  justb4/jmeter \
                  -n \
                  -t "/tests/Post Pet.jmx" \
                  -l /tests/resultados.jtl \
                  -j /tests/jmeter.log

                echo "Execução do JMeter finalizada."

                echo "Conteúdo do log do JMeter:"
                cat testes/jmeter.log

                echo "Arquivo de resultados:"
                ls -lh testes/resultados.jtl
                '''
            }
        }
    }

    post {
        always {
            echo 'Arquivando resultados do JMeter...'
            archiveArtifacts artifacts: 'testes/*.jtl,testes/*.log', fingerprint: true
        }

        success {
            echo 'Pipeline executado com sucesso.'
        }

        failure {
            echo 'Pipeline falhou. Verifique o console output e o arquivo jmeter.log.'
        }
    }
}
