pipeline {
    agent any
    environment {
        GITHUB_TOKEN = credentials('github-token')
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/funnydev2512/cicd-devops',
                    branch: 'main',
                    credentialsId: 'github-pat'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Build and Deploy') {
            steps {
                sh 'docker build -t my-backend-app .'
                sh 'docker stop $(docker ps -q) || true'
                sh 'docker run -d -p 3000:3000 my-backend-app'
            }
        }
    }
    post {
        success {
            updateGitHubStatus('success')
        }
        failure {
            updateGitHubStatus('failure')
        }
    }
}
void updateGitHubStatus(String status) {
    script {
        def commitSha = env.GIT_COMMIT
        def repoUrl = 'https://api.github.com/repos/funnydev2512/cicd-devops/statuses/' + commitSha
        def payload = [
            state: status,
            target_url: env.BUILD_URL,
            description: "Build ${status}",
            context: "ci/jenkins"
        ]
        sh "curl -X POST -H 'Authorization: token ${GITHUB_TOKEN}' -H 'Content-Type: application/json' -d '${payload.toString().replaceAll("'", "'\\''")}' ${repoUrl}"
    }
}