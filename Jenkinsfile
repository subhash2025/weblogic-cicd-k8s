pipeline {
    options {
        disableConcurrentBuilds()
    }
    agent any
    tools {
        jdk 'jdk11'
    }

    environment {
        WLSIMG_BLDDIR = "${env.WORKSPACE}/resources/build"
        WLSIMG_CACHEDIR = "${env.WORKSPACE}/resources/cache"
    }

    stages {
        stage ('Environment') {
            steps {
                sh '''
                    mkdir -p ${env.WORKSPACE}/resources/cache ${env.WORKSPACE}/resources/build
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "JAVA_HOME = ${JAVA_HOME}"
                    echo "IT Build DIR= ${WLSIMG_BLDDIR}"
                    echo "IT Cache DIR= ${WLSIMG_CACHEDIR}"
                '''
            }
        }
        stage ('Build') {
            steps {
                sh '''
                    curl -SLO  https://github.com/oracle/weblogic-image-tool/releases/download/release-1.6.0/imagetool.zip
                    jar xvf imagetool.zip
                    chmod +x imagetool/bin/*
                    ls -l imagetool/bin/*
                    imagetool/bin/setup.sh
                    imagetool/bin/imagetool.sh -V
                '''
            }
        }
     }
}
