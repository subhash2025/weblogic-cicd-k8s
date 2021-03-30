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
        IMAGE_NAME = "phx.ocir.io/weblogick8s/onprem-domain-image:1"
    }

    stages {
        stage ('Environment') {
            steps {
                sh '''
                    mkdir -p  ${WLSIMG_BLDDIR} ${WLSIMG_CACHE_DIR}
                    echo "IMAGE_NAME = ${IMAGE_NAME}"
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "JAVA_HOME = ${JAVA_HOME}"
                '''
            }
        }
        stage ('Build Image') {
            steps {
                sh '''
                    pwd
                    whoami
                    curl -SLO  https://github.com/oracle/weblogic-image-tool/releases/download/release-1.8.1/imagetool.zip
                    unzip -o ./imagetool.zip
                    rm -rf ${WLSIMG_CACHEDIR}
                    imagetool/bin/imagetool.sh cache addInstaller --type wdt --path /scratch/artifacts/imagetool/weblogic-deploy.zip --version 1.7.1
                    imagetool/bin/imagetool.sh cache addInstaller --type wls --path /scratch/artifacts/imagetool/fmw_12.2.1.4.0_wls_Disk1_1of1.zip --version 12.2.1.4.0
                    imagetool/bin/imagetool.sh cache addInstaller --type jdk --path /scratch/artifacts/imagetool/jdk-8u212-linux-x64.tar.gz --version 8u212
                    imagetool/bin/imagetool.sh create --tag ${IMAGE_NAME} --version 12.2.1.4.0 --jdkVersion=8u212 --wdtModel=./DiscoveredDemoDomain.yaml --wdtArchive=./DiscoveredDemoDomain.zip --wdtDomainHome=/u01/oracle/user_projects/domains/onprem-domain --wdtVariables=./DiscoverDemoDomain.properties --wdtVersion 1.7.1
                '''
            }
        }
        stage ('Push Image') {
            steps {
                sh '''
                    docker push ${IMAGE_NAME}
                '''
            }
        }
        stage('Roll Updates') {
            steps {
                sh '''
                    export KUBECONFIG=/scratch/k8s-demo/mrcluster_kubeconfig
                    export OCI_CLI_PROFILE=MONICA
                    export OCI_CONFIG_FILE=/var/lib/jenkins/.oci/config
                    export PATH=/var/lib/jenkins/bin:$PATH
                    kubectl apply -f ./domain.yaml
                '''
           }
      }
   }
   post {
    cleanup {
        deleteDir() /* clean up the workspace */
    }
  }
}
