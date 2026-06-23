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
        stage('Checkout TensorFlow') {
            steps {
                script {
                    // Shallow clone of TensorFlow repository
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
        
        stage('Checkout BoringSSL Folder') {
            steps {
                script {
                    // Checkout BoringSSL folder from scripts repository
                    dir('scripts-repo') {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: '*/master']],
                            extensions: [
                                [$class: 'SparseCheckoutPaths', 
                                 sparseCheckoutPaths: [[path: 'BoringSSL/*']]]
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
                    // Run your script here
                    // Example: sh 'bash scripts-repo/BoringSSL/your-script.sh'
                    echo 'Ready to run scripts from BoringSSL folder'
                    sh 'ls -la scripts-repo/BoringSSL/'
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
