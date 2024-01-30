pipeline {
    agent any

    environment {
        MAIN_BRANCH = 'main'
        NEW_BRANCH = 'release-1'
        REPOSITORIES = ['dev', 'prod', 'stg'] // Add your repository names here
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clone all repositories
                    checkout scm

                    // Additional setup if needed
                }
            }
        }

        stage('Create Branch') {
            steps {
                script {
                    // Iterate through repositories and create the new branch
                    REPOSITORIES.each { repository ->
                        createBranch(repository)
                    }
                }
            }
        }
    }
}

def createBranch(repository) {
    // Create a new branch in the specified repository
    sh "git checkout ${MAIN_BRANCH}"
    sh "git pull origin ${MAIN_BRANCH}"
    sh "git checkout -b ${NEW_BRANCH}"
    sh "git push origin ${NEW_BRANCH}"

    // Additional steps if needed
}
