pipeline {
    agent any

    stages {
        stage('Deploy Produccion') {
			environment {
				KBDir = credentials('MTKBDir')
				BatchDir = credentials('MTBatchDir')
				KBBatchDir = credentials('MTBatchKBDir')
				MTDir = credentials('MTProduccionDir')
			}
            when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
			parallel {
				stage('Deploy Web') {
					steps {
						build job: 'MT - Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBDir}"), text(name: 'DeployDestino', value: "${MTDir}")]
					}
				}
				stage('Deploy Batch') {
					steps {
						build job: 'MT - Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBBatchDir}"), text(name: 'DeployDestino', value: "${BatchDir}")]
					}
				}
			}
        }
		stage('Pruebas de carga') {
			steps {
				echo '----- Pruebas de carga -----'
			}
		}
    }
}