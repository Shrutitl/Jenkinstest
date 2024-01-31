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

                    // Iterate through repositories and create/push the new branch
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
        // Switch to the main branch
        sh "git checkout ${MAIN_BRANCH}"

        // Pull with rebase to reconcile divergent branches
        sh "git pull --rebase origin ${MAIN_BRANCH}"

        // Check if the branch already exists locally
        def branchExistsLocally = sh(script: "git branch --list ${NEW_BRANCH}", returnStatus: true) == 0

        if (!branchExistsLocally) {
            // Create and switch to the new branch locally
            sh "git checkout -b ${NEW_BRANCH}"
            
            // Add a dummy commit
            sh "touch dummy.txt"
            sh "git add dummy.txt"
            sh "git commit -m 'Initial commit'"
            
            // Push the new branch to the remote repository
            sh "git push origin ${NEW_BRANCH}"

            // Verify that the branch is now visible on GitHub
            sh "git ls-remote --heads origin ${NEW_BRANCH}"
        } else {
            // Branch already exists locally, push it to remote
            sh "git push origin ${NEW_BRANCH}"

            // Verify that the branch is now visible on GitHub
            sh "git ls-remote --heads origin ${NEW_BRANCH}"
        }
    }
    // Additional steps if needed
}
