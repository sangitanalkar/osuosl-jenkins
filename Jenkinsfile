pipeline {
    agent {
        docker {
            image 'osuosl/ubuntu-s390x:22.04'
            label 's390x-docker1'
        }
    }
    
    options {
        // Shallow clone for the main repository
        skipDefaultCheckout()
    }
    
    stages {
        stage('Checkout Envoy') {
            steps {
                script {
                    dir('envoy') {
						// Shallow clone of repository
						checkout([
							$class: 'GitSCM',
							branches: [[name: '*/master']],
							extensions: [
								[$class: 'CloneOption', depth: 1, noTags: false, shallow: true]
							],
							userRemoteConfigs: [[url: 'https://github.com/tensorflow/tensorflow.git']]
						])
                    }    
                }
            }
        }
        
        stage('Checkout Build files') {
            steps {
                script {
                    // Checkout OSUOSL-Jenkins/Tensorflow folder from scripts repository
                    dir('scripts-repo') {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/master']],
                            extensions: [
                                [$class: 'SparseCheckoutPaths', 
                                 sparseCheckoutPaths: [[path: 'Tensorflow/2.20.0/*']]]
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
                    sh '''
					pwd
					ls -l
                    ls -la scripts-repo/
                    ls -l scripts-repo/Tensorflow/2.20.0/
                    cp scripts-repo/Tensorflow/2.20.0//build_tensorflow.sh .
					cp -r scripts-repo/Tensorflow/2.20.0/patch .
					ls -l
					'''
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
