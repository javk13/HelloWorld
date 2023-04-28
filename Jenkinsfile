pipeline {
    agent any

     stages {
        stage('Get Code') {
            steps {
                // Obtener código del repo
                //git 'https://github.com/javk13/HelloWorld.git'
		//script {
			//scmVars = checkout scm (script no conocido)
			//echo 'scm : the commit id is ' + scmVars.GIT_COMMIT (script no conocido)
		}
            }
        }
        
        stage('Build') {
            steps {
                echo 'Que NOOOOO, python no compila código!!!'
		//echo 'El workspace contiene el commit \'' + scmVars.GIT_COMMIT + '\' de la rama \'' + scmVars.GIT_BRANCH + '\'' (Comando desconocido)
                echo WORKSPACE
                sh 'ls -la'
            }
        }
        
        stage('Tests') {
            parallel {
                stage('Unit') {
                    steps {
                        //catchError-captura errores para continuar pipeline y resto de etapas.
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                            sh '''
                            export PYTHONPATH=${WORKSPACE}
                            pytest-3 --junitxml=result-unit.xml test/unit
                            '''
                        }    
                    }
                }
                stage('Service') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh '''
                            export FLASK_APP=app/api.py
                            export FLASK_ENV=development
                            flask run &
                            java -jar /home/agent1/workspace/DevOps_Unir/test2/test/wiremock/wiremock-jre8-standalone-2.35.0.jar --port 9090 --root-dir /home/agent1/workspace/DevOps_Unir/test2/test/wiremock &
                            export PYTHONPATH=${WORKSPACE}
                            sleep 1; pytest-3 --junitxml=result-unit.xml test/rest                        
                            '''
                        //PING -n 21 127.0.0.1>nul (Desconocido-Sustituto "bien hecho" del comando sleep)
                        }
                    }    
                }
            }
        }
        stage ('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}
