// ============================================
// PACKAGE CONFIGURATION
// ============================================
// Set these variables based on the package you want to build
def PACKAGE_NAME = 'Tensorflow'  // Options: 'Tensorflow' or 'Envoy'
def REPO_ORG = 'tensorflow'      // Options: 'tensorflow' or 'envoyproxy'
def REPO_NAME = 'tensorflow'     // Options: 'tensorflow' or 'envoy'
// ============================================

pipeline {
    agent {
        docker {
            image 'osuosl/ubuntu-s390x:22.04'
            label 's390x-docker2'
        }
    }
    
    options {
        // Shallow clone for the main repository
        skipDefaultCheckout()
    }
    
    stages {
        stage("Checkout ${PACKAGE_NAME}") {
            steps {
                script {
                    dir(REPO_NAME.toLowerCase()) {
                        // Shallow clone of repository
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/master']],
                            extensions: [
                                [$class: 'CloneOption', depth: 1, noTags: false, shallow: true]
                            ],
                            userRemoteConfigs: [[url: "https://github.com/${REPO_ORG}/${REPO_NAME}.git"]]
                        ])
                    }    
                }
            }
        }
        
        stage('Checkout Build files') {
            steps {
                script {
                    // Checkout OSUOSL-Jenkins/{PACKAGE_NAME} folder from scripts repository
                    dir('scripts-repo') {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/master']],
                            extensions: [
                                [$class: 'SparseCheckoutPaths', 
                                 sparseCheckoutPaths: [[path: "Tensorflow/2.20.0/*"]]]
                            ],
                            userRemoteConfigs: [[url: 'https://github.com/linux-on-ibm-z/scripts.git']]
                        ])
                    }
                }
            }
        }
        
        stage('Run Script') {
            steps {
                script {
                    sh """
                        pwd
					ls -l
                    ls -la scripts-repo/
                    ls -l scripts-repo/Tensorflow/2.20.0/
                    cp scripts-repo/Tensorflow/2.20.0/build_tensorflow.sh .
					cp -r scripts-repo/Tensorflow/2.20.0/patch .
                    """
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}

