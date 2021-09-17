pipeline {
    agent any
    parameters {
        string(name: "APP_NAME", defaultValue: "co-com-pragma-api-eureka", description: "Nombre de nuestra app.")
        string(name: "APP_VERSION", defaultValue: "latest", description: "Version de nuestra app.")
        string(name: "CONTAINER_NAME", defaultValue: "api-eureka", description: "Nombre del contendor para nuestra app.")
        string(name: "APP_HOST_NAME", defaultValue: "eureka.privjaac.com", description: "DNS que apunta a nuestra app.")
        string(name: "APP_HOST_PORT", defaultValue: "91", description: "Puerto de arranque de nuestra app.")
        string(name: "APP_PATH", defaultValue: "", description: "Path principal de nuestra app.")
        string(name: "EUREKA_SERVER_HOST", defaultValue: "eureka.privjaac.com", description: "DNS que apunta a nuestro servidor eureka.")
        string(name: "PRI_DOCKER_HOST", defaultValue: "docker.privjaac.com", description: "DNS que apunta a nuestro servidor de docker-registry.")
        string(name: "PRI_DOCKER_USER", defaultValue: "jaac.docker", description: "Usuario de acceso para nuestro servidor de docker-registry.")
        string(name: "PRI_DOCKER_PASS", defaultValue: "jaac.docker", description: "Clave de acceso para nuestro servidor de docker-registry.")
    }
    tools {
        jdk 'JAVA_11'
        maven 'M2_3_6_3'
    }
    stages {
        stage('sonar-qube') {
            steps {
                sh '''
                echo 'simular y verificar pruebas de codigo con sonar qube.'
                '''
            }
        }
        stage('compilar-java-test') {
            steps {
                sh '''
                echo 'realizar pruebas unitarias en esta segmento.'
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
        stage('login-docker') {
            steps {
                sh '''
                docker login ${PRI_DOCKER_HOST} -u ${PRI_DOCKER_USER} -p ${PRI_DOCKER_PASS}
                '''
            }
        }
        stage('eliminar-contenedor-imagen') {
            steps {
                sh '''
                #!/bin/bash
                container_id=$(docker ps -aq --filter name=${CONTAINER_NAME})
                echo "$container_id"
                if [! -z $container_id]
                then
                    docker-compose -f dc-${CONTAINER_NAME}.yml up down
                fi
                image_id=$(docker images -q name=${PRI_DOCKER_HOST}/pragma/${APP_NAME}:${APP_VERSION})
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
                docker-compose -f dc-${CONTAINER_NAME}.yml build
                docker-compose -f dc-${CONTAINER_NAME}.yml push
                '''
            }
        }
        stage('ejecutar-contenedor-imagen') {
            steps {
                sh '''
                docker-compose -f dc-${CONTAINER_NAME}.yml up -d
                '''
            }
        }
        stage('logout-docker') {
            steps {
                sh '''
                docker logout
                '''
            }
        }
        stage('disponibilidad-contenedor') {
            steps {
                sh '''
                sleep 30s; curl -m 10 -s --head --request GET ${APP_HOST_NAME}:${APP_HOST_PORT}${APP_PATH}/actuator/health | grep 200
                '''
            }
        }
    }
}