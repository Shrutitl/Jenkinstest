pipeline {
    agent any

    environment {
        MAIN_BRANCH = 'Phase-2'
        NEW_BRANCH = 'release-4'
        ACCESS_TOKEN = credentials('githubtoken')
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
                    // Define repositories
                    REPOSITORIES = ['jenkinsstage', 'jenkinsprod', 'jenkinsdev']

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
    sh "git pull --rebase origin ${MAIN_BRANCH}"

    // Check if the branch already exists locally
    def branchExistsLocally = sh(script: "git branch --list ${NEW_BRANCH}", returnStatus: true) == 0

    if (!branchExistsLocally) {
        // Create and switch to the new branch locally
        sh "git checkout -b ${NEW_BRANCH}"

        // Additional steps if needed

        // Push the new branch to the remote repository
        def pushCommand = "git push origin ${NEW_BRANCH}"
        echo "Executing: ${pushCommand}"

        try {
            sh pushCommand
        } catch (Exception e) {
            echo "Failed to push the branch. Error: ${e.message}"
            currentBuild.result = 'FAILURE'
        }
    } else {
        echo "Branch ${NEW_BRANCH} already exists locally. Skipping branch creation."
    }

    // Additional steps if needed
}
