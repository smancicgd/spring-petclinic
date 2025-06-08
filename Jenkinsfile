pipeline {
    agent { label 'pipeline-agent' } 
    environment {
        // IS_PR = env.CHANGE_ID != null
        REPO = "${env.CHANGE_ID ? 'mr' : 'main'}"
        IMAGE = credentials('docker_image_name')
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
                script {
                    def GIT_COMMIT_SHORT = env.GIT_COMMIT.take(7)
                    echo "Git commit is ${GIT_COMMIT_SHORT}"
                }
            }
        }
    }
}