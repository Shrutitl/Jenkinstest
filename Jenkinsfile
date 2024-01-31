pipeline {
    agent any

    environment {
        MAIN_BRANCH = 'Phase-2'
        NEW_BRANCH = 'release-4'
        ACCESS_TOKEN = credentials('githubtoken')
        REPOSITORIES = ['jenkinsstage', 'jenkinsprod', 'jenkinsdev']
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clone all repositories
                    checkout([$class: 'GitSCM',
                               branches: [[name: "${MAIN_BRANCH}"]],
                               userRemoteConfigs: [[credentialsId: 'githubtoken', url: 'https://github.com/Shrutitl/jenkinsstage.git'],
                                                  [credentialsId: 'githubtoken', url: 'https://github.com/Shrutitl/jenkinsprod.git'],
                                                  [credentialsId: 'githubtoken', url: 'https://github.com/Shrutitl/jenkinsdev.git']]])

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
    dir(repository) {
        sh "git checkout ${MAIN_BRANCH}"
        sh "git pull --rebase origin ${MAIN_BRANCH}"

        // Check if the branch already exists locally
        def branchExistsLocally = sh(script: 'git branch --list ${NEW_BRANCH}', returnStatus: true) == 0

        if (!branchExistsLocally) {
            // Create and switch to the new branch locally
            sh "git checkout -b ${NEW_BRANCH}"
            // Additional steps if needed

            // Push the new branch to the remote repository
            sh "git push origin ${NEW_BRANCH}"
        } else {
            // Push the existing branch to the remote repository
            sh "git push origin ${NEW_BRANCH}"
            echo "Branch ${NEW_BRANCH} already exists locally. Pushing changes to remote."
        }

        // Additional steps if needed
    }
}
