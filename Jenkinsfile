pipeline {
    agent any
    tools {
        jdk 'JAVA_11'
        maven 'M2_3_6_3'
    }
    stages {
        stage('sonar-qube') {
            steps {
                sh '''
                echo 'aquí el codigo de simulación sonar qube'
                '''
            }
        }
        stage('compilar-java-test') {
            steps {
                sh '''
                echo 'aquí se realizan las pruebas unitarias.'
                '''
            }
        }
        stage('compilar-java') {
            steps {
                sh '''
                mvn clean install -Dmaven.test.skip=true
                '''
            }
        }
        stage('eliminar-contenedor-imagen') {
            steps {
                sh '''
                #!/bin/bash
                container_id=$(docker ps -aq --filter name=api_eureka)
                if [! -z $container_id]
                then
                    docker-compose -f dc-api-eureka.yml up down
                fi
                image_id=$(docker images -q name=ghcr.io/privjaac/co-com-pragma-api-eureka:latest)
                if [! -z $image_id]
                then
                    docker rmi $image_id
                fi
                '''
            }
        }
        stage('construir-subida-imagen') {
            steps {
                sh '''
                docker build -t ghcr.io/privjaac/co-com-pragma-api-eureka:latest .
                docker-compose -f dc-api-eureka.yml push
                '''
            }
        }
        stage('ejecutar-contenedor-imagen') {
            steps {
                sh '''
                docker-compose -f dc-api-eureka.yml up -d
                '''
            }
        }
        stage('disponibilidad-contenedor') {
            steps {
                sh '''
                sleep 30s; curl -m 10 -s --head --request GET api.privjaac.com:83/actuator/health || grep \\\'"200"\\\'
                '''
            }
        }
    }
}
