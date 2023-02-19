pipeline {
    agent any

    environment {
        BW_HOME = "/opt/tibco/bw/6.5"
        MVN_HOME = "/opt/maven"
        APP_NAME = "myapp"
        APP_VERSION = "1.0.0-SNAPSHOT"
        APP_GROUP_ID = "com.example.myapp"
        APP_PACKAGE_NAME = "${APP_GROUP_ID}.${APP_NAME}-${APP_VERSION}.ear"
        APP_DEPLOYMENT_XML = "deployment.xml"
    }

    stages {
        stage('Build') {
            steps {
                dir('myapp') {
                    sh "${MVN_HOME}/bin/mvn clean package"
                }
            }
        }

        stage('Create deployment artifacts') {
            steps {
                dir('myapp/target') {
                    sh "${BW_HOME}/bwtoolkit/jar/ear/packager -a ${APP_NAME} -v ${APP_VERSION} -o ${APP_PACKAGE_NAME} *-bwapp.ear"
                    sh "cp ${APP_PACKAGE_NAME} ../${APP_PACKAGE_NAME}"
                }

                dir('myapp') {
                    sh "${BW_HOME}/bwadmin/appmanage -add -name ${APP_NAME} -version ${APP_VERSION} -ear ../${APP_PACKAGE_NAME} -xml ${APP_DEPLOYMENT_XML}"
                }
            }
        }

        stage('Deploy to Tibco') {
            steps {
                sh "${BW_HOME}/bwadmin/appmanage -start -name ${APP_NAME} -version ${APP_VERSION}"
            }
        }
    }
}
