pipeline {
    agent any
    
    stages {
        stage('Get Code') {
            steps {
                // Obtener código del repo con comando jenkis del plugin github
                //git 'https://github.com/javk13/helloworld'
            }    
        } 
        
        stage('Build') {
            steps {
                echo 'Que NOOOOO, python no compila código'
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
                        //'/var/jenkins_home/workspace/DevOps_Unir/test2'  Es la ruta del workspace 
                            sh '''
                            export PYTHONPATH=${WORKSPACE}
                            pytest-3 --junitxml=result-unit.xml test/unit
                            '''
                        }
                    }    
                }
                stage('Service') {
                    steps {
                        // -NO ME FUNCIONA:java -jar '${WORKSPACE}/wiremock-jre8-standalone-2.35.0.jar' --port 9090 --root-dir '${WORKSPACE}/wiremock' & 
                        // - ME FUNCIONA: java -jar /home/agent1/workspace/DevOps_Unir/test2/test/wiremock/wiremock-jre8-standalone-2.35.0.jar --port 9090 --root-dir /home/agent1/workspace/DevOps_Unir/test2/test/wiremock &
                        // --port 9090 --root-dir '/var/jenkins_home/workspace/DevOps_Unir/test2/test/wiremock' $
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                            sh '''
                            export FLASK_APP=app/api.py
                            export FLASK_ENV=development
                            flask run &
                            java -jar /home/agent1/workspace/DevOps_Unir/test2/test/wiremock/wiremock-jre8-standalone-2.35.0.jar --port 9090 --root-dir /home/agent1/workspace/DevOps_Unir/test2/test/wiremock &
                            export PYTHONPATH=${WORKSPACE}
                            sleep 1; pytest-3 --junitxml=result-unit.xml test/rest
                            '''
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