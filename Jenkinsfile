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
                    sh 'pwd'
					sh 'ls -l'
                    sh 'ls -la scripts-repo/'
                    sh 'ls -l scripts-repo/Tensorflow/2.20.0/'
                    sh 'cp scripts-repo/Tensorflow/2.20.0//build_tensorflow.sh .'
					sh 'cp -r scripts-repo/Tensorflow/2.20.0/patch patch/ .'
					//sh 'bash build_tensorflow.sh -y'
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
