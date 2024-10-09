library 'my-shared-library'
 
pipeline {
    agent any
 
    options {
        timeout( time: 2, unit: 'HOURS' )
        buildDiscarder( logRotator( numToKeepStr: '20' ) )
    }
   
    environment {
        MNEMONIC                       = ''
        PRIMARY_BRANCH                 = 'main'
        MODULE                         = ''
        GROUP                          = ''
        ARTIFACTORY_CRED_ID            = ''
        ARTIFACTORY_REPO_URL           = ''
        ARTIFACTORY_UPLOAD_PATH        = ''
        MODEL_FRAMEWORK                = ''
        SSHKEYID                       = ''
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
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }            
            steps {
                script {
                    try {
                        get_artifact()
                    } catch (Exception e) {
                        error "Error with stage Get Artifact: ${e.message}"
                    }
                }
            }
        }
 
        stage('Validate Hash') {
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }
            steps {
                script {
                    try {
                        validate_hash()
                    } catch (Exception e) {
                        error "Validate Hash Stage Failed: ${e.message}"
                    }
                }
            }
        }
 
 
        stage('Upload Artifact') {
            when { expression { env.GIT_BRANCH == env.PRIMARY_BRANCH } }
            steps {
                script {
                    try {
                        upload_artifact()
                    } catch (Exception e) {
                        error "Error with stage Upload Artifact: ${e.message}"
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
        }
    }
 
    post {
        always {
            generalCleanup()
        }
    }
}
