pipeline {
    agent { label 'agent2' } 
    
    stages {
        stage ('Checkstyle') {
            when {
                expression { return env.CHANGE_ID != null }
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
        // stage ('Test') {
        //     when {
        //         expression { return env.CHANGE_ID != null }
        //     }
        //     environment {
        //         DATABASE_USER = credentials('DB_USER')
        //         DATABASE_PASS = credentials('DB_PASS')
        //         DATABASE = credentials('DB')
        //         DATABASE_URL = credentials('DB_URL')
        //     }
        //     steps {
        //         sh './mvnw clean test -X -Dspring.profiles.active=postgres -B'
        //     }
        // }
        stage ('Build') {
            when {
                expression { return env.CHANGE_ID != null }
            }
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }
        stage ('Create a docker image') {
            when {
                anyOf {
                    expression { return env.CHANGE_ID != null }
                    branch 'main'
                }
            }

            environment {
                REPO = "${env.CHANGE_ID != null ? 'mr' : 'main'}"
                IMAGE_TAG = env.GIT_COMMIT.take(7)
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