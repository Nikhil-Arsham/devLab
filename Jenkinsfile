pipeline {
    agent any

    environment {
        APP_NAME = "hello-java"
        VERSION  = "1.0.${BUILD_NUMBER}"
    }

    stages {

        stage('Compile Java') {
            steps {
                sh '''
                    rm -rf build package *.jar
                    mkdir -p build
                    javac -d build src/Hello.java
                    jar cfe hello.jar Hello -C build .
                '''
            }
        }

        stage('Prepare Package Layout') {
            steps {
                sh '''
                    mkdir -p package/usr/local/bin
                    cp hello.jar package/usr/local/bin/hello-java.jar
                '''
            }
        }

        stage('Build DEB using FPM') {
            steps {
                sh '''
                    fpm -s dir -t deb \
                        -n ${APP_NAME} \
                        -v ${VERSION} \
                        --description "Hello Java app packaged via Jenkins and FPM" \
                        package/
                '''
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: '*.deb', fingerprint: true
        }
    }
}