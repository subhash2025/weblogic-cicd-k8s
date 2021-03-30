
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
        IMAGE_NAME = "customimage2021/onprem-domain-image:1"
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
                    cp -R /var/lib/jenkins/backup/* .
                    ls -lrat
                    curl -SLO  https://github.com/oracle/weblogic-image-tool/releases/download/release-1.8.1/imagetool.zip
                    unzip -o ./imagetool.zip
                    rm -rf ${WLSIMG_CACHEDIR}
                    imagetool/bin/imagetool.sh cache addInstaller --type wdt --path ./weblogic-deploy.zip --version 1.7.1
                    imagetool/bin/imagetool.sh cache addInstaller --type wls --path ./fmw_12.2.1.4.0_infrastructure_Disk1_1of1.zip --version 12.2.1.4.0
                    imagetool/bin/imagetool.sh cache addInstaller --type jdk --path ./jdk-8u212-linux-x64.tar.gz --version 8u212
                    imagetool/bin/imagetool.sh create --tag ${IMAGE_NAME} --version 12.2.1.4.0 --jdkVersion=8u212 --wdtModel=./DiscoveredDemoDomain.yaml --wdtArchive=./DiscoveredDemoDomain.zip --wdtDomainHome=/u01/oracle/user_projects/domains/onprem-domain --wdtVariables=./DiscoverDemoDomain.properties --wdtVersion 1.7.1
                '''
            }
        }
        stage ('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
                sh '''
                    docker login -u ${DOCKER_REGISTRY_USER} -p ${DOCKER_REGISTRY_PWD}
                    docker push ${IMAGE_NAME}
            
                '''
            }
            }
        }
        stage('Roll Updates') {
            steps {
                sh '''
                    sed -i s#{{docker_image}}#${IMAGE_NAME}#g domain.yaml
                    cat domain.yaml
                    aws eks --region us-east-1 update-kubeconfig --name eks-tools-cicd
                    #export KUBECONFIG=/scratch/k8s-demo/mrcluster_kubeconfig
                    #export PATH=/var/lib/jenkins/bin:$PATH
                    #kubectl apply -f ./domain.yaml
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
