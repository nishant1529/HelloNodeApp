node {
    def commit_id

    stage('Preparation') {
        checkout scm

        commit_id = bat(
            script: 'git rev-parse --short HEAD',
            returnStdout: true
        ).trim()

        echo "Commit: ${commit_id}"
    }

    stage('Test') {
        nodejs(nodeJSInstallationName: 'nodejs') {
            bat 'npm ci'
            bat 'npm test'
        }
    }

    stage('Docker Build') {
        def app = docker.build("nishant1630/hello-nodejs:${commit_id}")
    }

    stage('Docker Push') {
        docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
            docker.image("nishant1630/hello-nodejs:${commit_id}").push()
            docker.image("nishant1630/hello-nodejs:${commit_id}").push("latest")
        }
    }
}