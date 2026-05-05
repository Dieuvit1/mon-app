stage('Export HTML Static') {
    steps {
        sh '''
            docker create --name build-container-${BUILD_NUMBER} ${IMAGE_NAME}:${BUILD_NUMBER}
            docker start -a build-container-${BUILD_NUMBER}
            docker cp build-container-${BUILD_NUMBER}:/app/out ./out
            docker rm build-container-${BUILD_NUMBER}
        '''
    }
}