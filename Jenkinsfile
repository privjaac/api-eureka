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
        stage('compilación-java-test') {
            steps {
                sh '''
                echo 'aquí se realizan las pruebas unitarias.'
                '''
            }
        }
        stage('compilación-java') {
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
                echo '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' | docker login ghcr.io -u privjaac --password-stdin
                docker-compose -f dc-api-eureka.yml up down
                container_id=$(docker images -q name=ghcr.io/privjaac/co-com-pragma-api-eureka:latest)
                docker rmi $container_id
                '''
            }
        }
        stage('construir-subida-imagen') {
            steps {
                sh '''
                echo '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' | docker login ghcr.io -u privjaac --password-stdin
                #docker-compose -f dc-api-eureka.yml build
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
