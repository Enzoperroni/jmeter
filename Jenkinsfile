pipeline {
    agent any

    environment {
        JMETER_HOME = '/opt/apache-jmeter-5.6.3'
        PATH = "${JMETER_HOME}/bin:${env.PATH}"
    }

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

        stage('Instalar JMeter se necessário') {
            steps {
                sh '''
                if [ ! -d "$JMETER_HOME" ]; then
                  echo "Baixando JMeter..."
                  apt-get update
                  apt-get install -y wget unzip default-jre
                  wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-5.6.3.tgz
                  tar -xzf apache-jmeter-5.6.3.tgz
                  mv apache-jmeter-5.6.3 /opt/apache-jmeter-5.6.3
                else
                  echo "JMeter já instalado."
                fi

                jmeter -v
                '''
            }
        }

        stage('Rodar teste JMeter') {
            steps {
                sh '''
                echo "Iniciando execução do JMeter..."

                jmeter \
                  -n \
                  -t "testes/Post Pet.jmx" \
                  -l "testes/resultados.jtl" \
                  -j "testes/jmeter.log"

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
            archiveArtifacts artifacts: 'testes/*.jtl,testes/*.log', fingerprint: true
        }
    }
}
