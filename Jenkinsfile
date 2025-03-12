pipeline {
    agent any

    environment {
        DOCKER_IMAGE_WEB = "fastapi-example-web-1"
        DOCKER_IMAGE_DB = "fastapi-example-db-1"
        DOCKER_TAG = "latest"
        DOCKER_REGISTRY = "arwindersingh82"
        LOCAL_DOCKER_HOST = "tcp://dockserv:2375"
        GIT_REPO = "https://github.com/arwindersingh82/fastApi-example.git"
        DOCKER_SERVER = "root@dockserv"
        SSH_COMMAND = "ssh -o StrictHostKeyChecking=no ${DOCKER_SERVER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/arwindersingh82/fastApi-example.git'
            }
        }

        stage('Remove old image') {
            steps {
                sshagent(['arnieAsusMainKey']) {
                    sh """
                    ${SSH_COMMAND} "
                    docker stop ${DOCKER_IMAGE_WEB} || true && \
                    docker stop ${DOCKER_IMAGE_DB} || true && \
                    docker rm -f ${DOCKER_IMAGE_WEB} || true && \
                    docker rm -f ${DOCKER_IMAGE_DB} || true
                    "
                    """
                }
            }
        }

        stage('Checkout the Latest Code from GitHub') {
            steps {
                sshagent(['arnieAsusMainKey']) {
                    sh """
                    ${SSH_COMMAND} "
                    cd /root/ && git clone ${GIT_REPO} || true && \
                    cd /root/fastApi-example && git pull origin master
                    "
                    """
                }
            }
        }

        stage('Run the docker image') {
            steps {
                sshagent(['arnieAsusMainKey']) {
                    sh """
                    ${SSH_COMMAND} "cd /root/fastApi-example/ && \
                    docker compose up -d --build"
                    """
                }
            }
        }
        }
}
