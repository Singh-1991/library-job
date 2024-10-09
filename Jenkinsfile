library 'my-shared-library'
 
pipeline {
    agent any
 
    options {
        timeout( time: 2, unit: 'HOURS' )
        buildDiscarder( logRotator( numToKeepStr: '20' ) )
    }
   
    environment {
        MNEMONIC                       = "ebd" // make sure this is all lowercase
        PRIMARY_BRANCH                 = "validate-hash"
        MODULE                         = "ebd-fraaml-demo"
        GROUP                          = "pnc.ebd.fraaml"
        ARTIFACTORY_CRED_ID            = "jnk-serviceid-prod"
        ARTIFACTORY_REPO_URL           = "https://rpo.pncint.net/artifactory"
        ARTIFACTORY_UPLOAD_PATH        = "generic-stage-local/pnc/ebd"
        MODEL_FRAMEWORK                = "xgboost-model"
        SSHKEYID                       = "nana-ssh" //"jnk-gitid"
        TARBALL                        = ''
    }
 
    stages {

        stage('Verify Subscriber Count and Pattern') {
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }
            steps {
                script {
                    verify_subscriber()
                }
            }
        }
 
        stage('Get Artifact') {
            steps {
                container('awscli-image') {
                    script {
                        try {
                            get_artifact()
                        } catch (Exception e) {
                            error "Error with stage Get Artifact: ${e.message}"
                        }
                    }
                }
            }
        }
 
        stage('Validate Hash') {
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }
            steps {
                container('awscli-image') {
                    script {
                        try {
                            validate_hash()
                        } catch (Exception e) {
                            error "Validate Hash Stage Failed: ${e.message}"
                        }
                    }
                }
            }
        }
 
 
        /*
        stage('Upload Artifact') {
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }
            steps {
                container('build-tools') {
                    script {
                        try {
                            upload_artifact()
                        } catch (Exception e) {
                            error "Error with stage Upload Artifact: ${e.message}"
                        }
                    }
                }
            }
        }
 
        stage('Update Manifest') {
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }
            steps {
              script {
                  try {
                      update_manifest()
                  } catch (Exception e) {
                      error "Error with stage Update Manifest: ${e.message}"
                  }
              }  
            }
        }*/
    }
 
    post {
        always {
            generalCleanup()
        }
    }
}
