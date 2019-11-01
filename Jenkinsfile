pipeline {
    options {
        disableConcurrentBuilds()
    }
    agent any
    tools {
        jdk 'jdk11'
    }

    environment {
        WLSIMG_BLDDIR = "${env.WORKSPACE}/imagetool/target/build"
        WLSIMG_CACHEDIR = "${env.WORKSPACE}/imagetool/target/cache"
    }

    stages {
        stage ('Environment') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "JAVA_HOME = ${JAVA_HOME}"
                    echo "IT Build DIR= ${WLSIMG_BLDDIR}
                    echo "IT Cache DIR= ${WLSIMG_CACHEDIR}
                '''
            }
        }
        stage ('Build') {
            steps {
                sh '''
                    curl --silent "https://api.github.com/repos/oracle/weblogic-image-tool/releases/latest" |
                    grep '"tag_name":' |sed -E 's/.*"([^"]+)".*/\1/' | 
                    xargs -I {} curl -sOL "https://github.com/oracle/weblogic-image-tool/releases/download/"{}'/imagetool.zip'
                    jar xvf imagetool.zip
                    chmod +x imagetool/bin/*
                    ls -l imagetool/bin/*
                    imagetool/bin/setup.sh
                    imagetool/bin/imagetool.sh  --V
                '''
            }
        }
     }
}
