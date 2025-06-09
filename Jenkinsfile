pipeline {
    agent { label 'pipeline-agent' } 
    
    stages {
        stage ('Checkstyle') {
            when {
                expression { return env.CHANGE_ID != null}
            }
            steps {
                echo "Executing checkstyle ${env.CHANGE_ID} ${env.BRANCH_NAME}"
                // sh './mvnw checkstyle:checkstyle'
            }
            // post {
            //     always {
            //         archiveArtifacts artifacts: 'target/checkstyle-result.xml', fingerprint: true
            //     }
            // }
        }
        stage ('Test') {
            when {
                expression { return env.CHANGE_ID != null}
            }
            steps {
                echo "Executing tests ${env.CHANGE_ID} ${env.BRANCH_NAME}"

                // sh './mvnw test -B'
            }
        }
        stage ('Build') {
            when {
                expression { return env.CHANGE_ID != null}
            }
            steps {
                echo "Executing build ${env.CHANGE_ID} ${env.BRANCH_NAME}"

                // sh './mvnw clean package -DskipTests'
            }
        }
        stage ('Push docker image') {
            environment {
                REPO = "${env.CHANGE_ID != null ? 'mr' : 'main'}"
                
                IMAGE = credentials('docker_image_name')
                IMAGE_TAG = "${env.BRANCH_NAME == 'main' ? 'latest' : GIT_COMMIT_SHORT}"

                GIT_COMMIT_SHORT = env.GIT_COMMIT.take(7)

                //add nexus url depending on the repo
                //withcredentials for nexus do the docker build
            }
            steps {
                echo "commit short ${GIT_COMMIT_SHORT} ${REPO} ${env.CHANGE_ID} ${env.BRANCH_NAME} ${IMAGE_TAG}"
            }
        }
    }
}