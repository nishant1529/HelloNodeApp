node {
    def commit_id

    stage('Preparation') {
        checkout scm

        def output = bat(
            script: '@git rev-parse --short HEAD',
            returnStdout: true
        ).trim()

        // Get the last non-empty line, which is the commit hash
        commit_id = output.readLines().last().trim()

        echo "Commit ID: ${commit_id}"
    }

    stage('Test') {
        nodejs(nodeJSInstallationName: 'nodejs') {
            bat 'npm ci'
            bat 'npm test'
        }
    }

    stage('Docker Test') {
        bat 'docker version'
    }

    stage('Docker Build') {
        bat "docker build -t nishant1630/hello-nodejs:${commit_id} ."
    }

    stage('Docker Login') {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub',
                usernameVariable: 'nishant1630',
                passwordVariable: 'Docker@1234'
            )
        ]) {
            bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
        }
    }

    stage('Docker Push') {
        bat "docker push nishant1630/hello-nodejs:${commit_id}"
        bat "docker tag nishant1630/hello-nodejs:${commit_id} nishant1630/hello-nodejs:latest"
        bat "docker push nishant1630/hello-nodejs:latest"
    }
}