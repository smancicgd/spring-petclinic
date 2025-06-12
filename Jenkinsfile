pipeline {
    agent { label 'pipeline-agent' } 
    
    stages {
        stage ('Checkstyle') {
            when {
                expression { return env.CHANGE_ID != null }
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
                expression { return env.CHANGE_ID != null }
            }
            steps {
                echo "Executing tests ${env.CHANGE_ID} ${env.BRANCH_NAME}"

                // sh './mvnw test -B'
            }
        }
        stage ('Build') {
            when {
                expression { return env.CHANGE_ID != null }
            }
            steps {
                echo "Executing build ${env.CHANGE_ID} ${env.BRANCH_NAME}"

                // sh './mvnw clean package -DskipTests'
            }
        }
        stage ('Push docker image') {
            when {
                anyOf {
                    expression { return env.CHANGE_ID != null }
                    branch 'main'
                }
            }

            environment {
                REPO = "${env.CHANGE_ID != null ? 'mr' : 'main'}"
                GIT_COMMIT_SHORT = env.GIT_COMMIT.take(7)
                IMAGE_TAG = "${env.BRANCH_NAME == 'main' ? 'latest' : GIT_COMMIT_SHORT}"
            }

            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "docker", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "docker build -t ${DOCKER_USER}/${REPO}:${IMAGE_TAG} -f Dockerfile.multistage ."
                        sh "docker login -u $DOCKER_USER -p $DOCKER_PASS"
                        sh "docker push ${DOCKER_USER}/${REPO}:${IMAGE_TAG}"
                    }

                }
            }
        }
    }
}