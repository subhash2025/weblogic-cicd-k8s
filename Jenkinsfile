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
                    mkdir -p  ${WLSIMG_BLDDIR} ${WLSIMG_CACHE_DIR}
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "JAVA_HOME = ${JAVA_HOME}"
                '''
            }
        }
        stage ('Build') {
            steps {
                sh '''
                    curl -SLO  https://github.com/oracle/weblogic-image-tool/releases/download/release-1.6.0/imagetool.zip
                    jar xvf imagetool.zip
                    chmod +x imagetool/bin/*
                    imagetool/bin/setup.sh
                    imagetool/bin/imagetool.sh cache addInstaller --type wdt --path /scratch/artifacts/imagetool/weblogic-deploy.zip --version 1.1.1
                    imagetool/bin/imagetool.sh cache addInstaller --type wls --path /scratch/artifacts/imagetool/fmw_12.2.1.3.0_wls_Disk1_1of1.zip --version 12.2.1.3.0
                    imagetool/bin/imagetool.sh cache addInstaller --type jdk --path /scratch/artifacts/imagetool/jdk-8u212-linux-x64.tar.gz --version 8u212 
                    imagetool/bin/imagetool.sh cache listItems 
                '''
            }
        }
     }
}
