pipeline {
    agent { label 'pipeline-agent' } 
    environment {
        REPO = "${env.CHANGE_ID != null ? 'mr' : 'main'}"
        IMAGE = credentials('docker_image_name')
        GIT_COMMIT_SHORT = env.GIT_COMMIT.take(7)
        IMAGE_TAG = "${env.BRANCH_NAME == 'main' ? 'latest' : GIT_COMMIT_SHORT}"
    }
    stages {
        stage ('Checkstyle') {
            when {
                expression { return env.CHANGE_ID != null}
            }
            steps {
                sh './mvnw checkstyle:checkstyle'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'target/checkstyle-result.xml', fingerprint: true
                }
            }
        }
        stage ('Test') {
            when {
                expression { return env.CHANGE_ID != null}
            }
            steps {
                sh './mvnw test -B'
            }
        }
        stage ('Build') {
            when {
                expression { return env.CHANGE_ID != null}
            }
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }
        stage ('Push docker image') {
            steps {
                echo "commit short ${GIT_COMMIT_SHORT} ${REPO} ${env.CHANGE_ID} ${env.BRANCH_NAME} ${IMAGE_TAG}"
            }
        }
    }
}