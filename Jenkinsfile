pipeline {
    agent any

    stages {
		stage('Clean Batch'){
			environment {
				BatchDir = credentials('MTBatchDir')
				MTUser = credentials('MTIISUser')
			}
			steps {
				build job: 'Clean', parameters: [text(name: 'Directorio', value: "${BatchDir}"), text(name: 'Usuario', value: "${MTUser}")]
			}
		}
		stage('Clean Produccion'){
			environment {
				MTDir = credentials('MTProduccionDir')
				MTUser = credentials('MTIISUser')
			}
			steps {
				build job: 'Clean', parameters: [text(name: 'Directorio', value: "${MTDir}"),  text(name: 'Usuario', value: "${MTUser}")]
			}
		}
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
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBDir}\Pro\web\"), text(name: 'DeployDestino', value: "${MTDir}")]
					}
				}
				stage('Deploy Batch') {
					steps {
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBBatchDir}"), text(name: 'DeployDestino', value: "${BatchDir}")]
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