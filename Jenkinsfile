node {
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
        IMAGE_TAG = "phx.ocir.io/weblogick8s/onprem-domain-image:2.0"
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
        stage ('Build Image') {
            steps {
                sh '''
                    curl -SLO  https://github.com/oracle/weblogic-image-tool/releases/download/release-1.6.0/imagetool.zip
                    jar xvf imagetool.zip
                    chmod +x imagetool/bin/*
                    rm -rf ${WLSIMG_CACHEDIR}
                    imagetool/bin/imagetool.sh cache addInstaller --type wdt --path /scratch/artifacts/imagetool/weblogic-deploy.zip --version 1.1.1
                    imagetool/bin/imagetool.sh cache addInstaller --type wls --path /scratch/artifacts/imagetool/fmw_12.2.1.4.0_wls_Disk1_1of1.zip --version 12.2.1.4.0
                    imagetool/bin/imagetool.sh cache addInstaller --type jdk --path /scratch/artifacts/imagetool/jdk-8u212-linux-x64.tar.gz --version 8u212
                    imagetool/bin/imagetool.sh create --tag ${IMAGE_TAG} --version 12.2.1.4.0 --jdkVersion=8u212 --wdtArchive=./DiscoveredDemoDomain.zip --wdtModel=./DiscoverDomain-v1.yaml --wdtDomainHome=/u01/oracle/user_projects/domains/onprem-domain --wdtVariables=./domain.properties --wdtVersion=1.1.1
                '''
            }
        }
        stage ('Push Image') {
            steps {
                sh '''
                    docker push ${IMAGE_TAG}
                    docker rmi ${IMAGE_TAG} 
                '''
            }
        }
        stage ('Roll Change') {
            steps {
                sh '''
                    ls domain.yaml
                '''
            }
        }
        stage('List pods') {
          withKubeConfig(caCertificate: '''-----BEGIN CERTIFICATE-----
          MIIDjDCCAnSgAwIBAgIUQNdaHQuKwqO6Hmd4i4UsE/AY31UwDQYJKoZIhvcNAQEL
          BQAwXjELMAkGA1UEBhMCVVMxDjAMBgNVBAgTBVRleGFzMQ8wDQYDVQQHEwZBdXN0
          aW4xDzANBgNVBAoTBk9yYWNsZTEMMAoGA1UECxMDT0RYMQ8wDQYDVQQDEwZLOFMg
          Q0EwHhcNMTgxMjEwMTY1OTAwWhcNMjMxMjA5MTY1OTAwWjBeMQswCQYDVQQGEwJV
          UzEOMAwGA1UECBMFVGV4YXMxDzANBgNVBAcTBkF1c3RpbjEPMA0GA1UEChMGT3Jh
          Y2xlMQwwCgYDVQQLEwNPRFgxDzANBgNVBAMTBks4UyBDQTCCASIwDQYJKoZIhvcN
          AQEBBQADggEPADCCAQoCggEBAKNLLFnWeV7nWkTAqyLJlvSSdcHrhkoJgLGXQ/d/
          bV5udGmM2DomsC8bR0chXYTZWjC9T0x+wJrBQWQR6yFYxXViybctTQZBbkJAtaUb
          znFQ3HxUAPNZtsnLkMEDDPdz5PKk6wPvXjQ0knldZZSIx36IH7zMdIUrwnySm+51
          WZkEoKol5uVy8nAATh8UFDrxlqIBpuCu8pLO/l/HxhFCbzvsMwog+9O5fwRNKOeh
          FPqzoD6LEUP9VJa3b2FhRIG+3DE7Dr+1Exsdl+BnqOr6IEDQQ9ySX+78ZmPkUJ36
          p/cpNXROz+DhTSqJ1pNozRuNNF/5uh7CnnJM1enovZDGJgUCAwEAAaNCMEAwDgYD
          VR0PAQH/BAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFBYul9DjPHOk
          gsko6ei+38OUEcurMA0GCSqGSIb3DQEBCwUAA4IBAQAhluOif7Ogm0ntXIH5VWr2
          oHgyy2raOOVT3140jyKK/x1188f4vd2MXORPxpmDAyh6ZEm7xfphXHnCPudMXsK8
          IGyuOGc2eH9RsleeS+VFYYxWOibWtZHtvln0LV5numubln/smf8rK0KP6e1mCYa9
          AkOh1+gBLrUirlIJapWROwD1iUEEDBm5OW4fiVHvvResEsBrrx1vF8QvM8rIpG2d
          Dl5LQ82MG1CtYErwJELAo4A6bYeFd+fgvz5cRlovkP9P9PIwqytCmGLdxG7d+v+i
          7buDX85U50L73IkcpxjMEOxy8nPoBjRfEjLmvAivO/RBKWo4DBBFWieEE7+Fhj/M
          -----END CERTIFICATE-----''', 
         clusterName: 'cluster-czdmztemm3g', 
         contextName: 'context-czdmztemm3g', 
         credentialsId: 'mgr-kubeconfig-secret', 
         namespace: 'onprem-domain-ns', 
         serverUrl: 'https://czdmztemm3g.us-phoenix-1.clusters.oci.oraclecloud.com:6443') {
         // some block
         sh 'kubectl get pods'
         }
     }
  }
}
