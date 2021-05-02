version = BUILD_NUMBER
dockrepo = "klstg"
MOUNT_PATH = '/tmp/deploy/bundle'

pipeline {
    agent { 
      docker { 
        registryUrl 'https://klstg-docker.slb-wartifactory-v.stg.rmn.local'
        registryCredentialsId 'temp-pradeesh-kumar'          
        image 'kldev-docker.slb-wartifactory-v.stg.rmn.local/rakuten/b2b_bss/maven:3.6.3'
        args '-v $HOME/.m2:/root/.m2'
        label 'wjenkins-agent101zc-shared-1' 
      }
    }

    environment {
        PREFIX = '1.0.'
        SUFFIX = '-SNAPSHOT'
        
		
	
		//############ env. variable for CD
		
	//	ROBIN_API_URL = "https://[240b:c0e0:102:5e2b:b422:2::]:29465/api/v3/robin_server"
	    ROBIN_API_URL = "https://[240b:c0e0:104:5400:b432:2::]:29465/api/v3/robin_server"
	
	
		ROBIN_LOGIN_URL = "${ROBIN_API_URL}/login"
		ROBIN_APP_URL = "${ROBIN_API_URL}/apps/demoapp?info=true"
		ARTIFACTORY_URL = "https://slb-wartifactory-v.stg.rmn.local/artifactory"
		BUNDLE_PATH = "${ARTIFACTORY_URL}/Mobility-Solutions-Group/Vendors/Mavenir/B2B-BSS/Early-Releases/V1"
		APP_NAME = "api-gateway"
		MANIFEST_PATH = "${MOUNT_PATH}/robin/deploy/dev/"
		FILE_SERVER_URL = "https://[240b:c0e0:104:5400:b432:2::]:29465/api/v3/file_server"
		TENANT_1 = "tn_Mavenir_B2B-BSS-apps_staging3"
		TENANT_2 = "tn_Mavenir_ISP-BSS-apps_basic"
		REPOSITORY_DIR = "rakuten/b2b_bss";

		//###############
		
    }

    stages {

        stage('clone') {
            steps {
              git branch: 'master',credentialsId: 'temp-pradeesh-kumar', url: "https://slb-wgithub-v.stg.rmn.local/migration/dep_api-gateway.git"
               
            }
        }
        
        stage('Maven Clean') {
            steps {
              configFileProvider([configFile(fileId: 'cante-katana-maven', variable: 'MAVEN_SETTINGS_XML')]) {
                  sh '''
                    mvn -Dmaven.test.skip=True \
                        -Dmaven.wagon.http.ssl.insecure=true \
                        -Dmaven.wagon.http.ssl.allowall=true \
                        -Dmaven.wagon.http.ssl.ignore.validity.dates=true \
                        -s $MAVEN_SETTINGS_XML \
                        clean package -U --batch-mode  -Pdefault
                  '''
              }
            }
        }
        
         stage('Maven Build') {
             steps {
                configFileProvider([configFile(fileId: 'cante-katana-maven', variable: 'MAVEN_SETTINGS_XML')]) {
                    sh '''
                    mvn -Dmaven.test.skip=True \
                        -Dmaven.wagon.http.ssl.insecure=true \
                        -Dmaven.wagon.http.ssl.allowall=true \
                        -Dmaven.wagon.http.ssl.ignore.validity.dates=true \
                        -s $MAVEN_SETTINGS_XML \
                        compile
                    '''
                }

            }
        }
        stage("Deploy to Staging"){
            when {
                branch 'staging'
            }
            steps {
                echo "branch staging"
             }
            post{
                success{
                    echo "Successfully deployed to Staging"
                }
                failure{
                    echo "Failed deploying to Staging"
                }
            }
        }
    }

}
