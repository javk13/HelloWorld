pipeline {
    agent any
    
    stages {

        stage('Clean Workspace'){
		    steps{
			//limpiamos los directorios de trabajo, que se descarge la info
			    sh '''
				rm -fr $WORKSPACE/*
				ls -lrth $WORKSPACE/
			    '''
		    }
	    }

        stage('Get Code') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                // Obtener código del repo con comando jenkis del plugin github
                git 'https://github.com/javk13/HelloWorld.git'
                //script {
			//scmVars = checkout scm                (SCRIPT DESCONOCIDO)
			//echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
                }
            }    
        } 
        
        stage('Build') {
            steps {
                echo 'Que NOOOOO, python no compila código'
                echo WORKSPACE
                //echo 'El workspace contiene el commit \'' + scmVars.GIT_COMMIT + '\' de la rama \'' + scmVars.GIT_BRANCH + '\''     (DESCONOCIDO)
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
                            java -jar $WORKSPACE/test/wiremock-jre8-standalone-2.35.0.jar --port 9090 --root-dir $WORKSPACE/test/wiremock &
                            export PYTHONPATH=${WORKSPACE}
                            sleep 1; pytest-3 --junitxml=result-unit.xml test/rest
                            '''
                        //PING -n 21 127.0.0.1>nul   (Forma LIMPIA y desconocida de retrasar ejecución pytest)
                        }
                    }
                }
            }
        }
        
        stage('Results') 
        {
            steps {
                junit 'result*.xml'
            }    
        }
    }
}