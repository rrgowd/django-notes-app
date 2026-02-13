@Library("Shared") _

pipeline {
    agent { label "vinod" }

    environment {
        IMAGE_NAME = "rrgowd/notes-aap"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }
    stages {

        stage("Hello") {
            steps {
                script {
                    hello()
                }
            }
        }
        stage("Code") {
            steps {
                script {
                    clone('https://github.com/rrgowd/django-notes-app.git', 'main')
                }
            }
        }

        stage("Build") {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage("Deploy") {
            steps {
                sh """
                    docker rm -f notesapp || true
                    docker run -d --name notesapp -p 8000:8000 ${IMAGE_NAME}:${IMAGE_TAG} \
                    python3 manage.py runserver 0.0.0.0:8000
                """
            }
        }

        stage("Push") {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}
