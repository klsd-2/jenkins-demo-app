        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:${BUILD_NUMBER}

                    # wait up to 30s for the app to answer on IPv4
                    for i in $(seq 1 15); do
                        if docker exec ${CONTAINER_NAME} wget -qO- http://127.0.0.1:3000/health; then
                            echo "App is healthy"
                            exit 0
                        fi
                        echo "Waiting for app... ($i)"
                        sleep 2
                    done

                    echo "App did not become healthy. Container logs:"
                    docker logs ${CONTAINER_NAME}
                    exit 1
                '''
            }
        }