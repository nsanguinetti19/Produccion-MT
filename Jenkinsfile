pipeline {
    agent any

    stages {
		stage('Clean Batch'){
			environment {
				BatchDir = credentials('MTBatchDir')
				MTUser = credentials('MTIISUser')
			}
			steps {
				build job: 'Clean', parameters: [text(name: 'Directorio', value: "${BatchDir}"),  text(name: 'Usuario', value: "${MTUser}"), text(name: 'ComputerName', value: "EVA")]
			}
		}
		stage('Clean Backup Anterior'){
			environment {
				MTBackupDir = credentials('MTBackupDir')
				MTUser = credentials('MTIISUser')
			}
			steps {
				build job: 'Clean', parameters: [text(name: 'Directorio', value: "${MTBackupDir}"),  text(name: 'Usuario', value: "${MTUser}"), text(name: 'ComputerName', value: "EVA")]
			}
		}
		
		stage('Deploy Backup') {
			environment {
				MTDir = credentials('MTProduccionDir')
				MTBackupDir = credentials('MTBackupDir')
			}
			when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
			stage('Deploy Web') {
				steps {
					build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${MTDir}"), text(name: 'DeployDestino', value: "${MTBackupDir}")]
				}
			}
			}
		stage('Clean Produccion'){
			environment {
				MTDir = credentials('MTProduccionDir')
				MTUser = credentials('MTIISUser')
			}
			steps {
				build job: 'Clean', parameters: [text(name: 'Directorio', value: "${MTDir}"),  text(name: 'Usuario', value: "${MTUser}"), text(name: 'ComputerName', value: "EVA")]
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
						build job: 'Deploy', parameters: [text(name: 'DeployOrigen', value: "${KBDir}\\Pro\\web\\"), text(name: 'DeployDestino', value: "${MTDir}")]
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