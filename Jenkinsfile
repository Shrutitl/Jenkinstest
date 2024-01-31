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
                    // Define repositories and corresponding email addresses
                    def repositoryEmails = [
                        'jenkinsstage': 'Shrutitl@dropamailtoshruti@gmail.com',
                        'jenkinsprod': 'Shrutitl@dropamailtoshruti@gmail.com',
                        'jenkinsdev': 'Shrutitl@dropamailtoshruti@gmail.com'
                    ]

                    // Iterate through repositories
                    repositoryEmails.each { repository, userEmail ->
                        createBranch(repository, userEmail)
                    }
                }
            }
        }
    }
}


def createBranch(repository, userEmail) {
    script {
        // Set Git user email for the specific repository
        sh "git -C ${repository} config user.email '${userEmail}'"

        // Check if the branch already exists locally
        def branchExistsLocally = sh(script: "git -C ${repository} branch --list ${NEW_BRANCH}", returnStatus: true) == 0

        if (!branchExistsLocally) {
            // Create and switch to the new branch in the specified repository
            sh "git -C ${repository} checkout -b ${NEW_BRANCH}"

            // Pull with rebase to reconcile divergent branches
            sh "git -C ${repository} pull --rebase origin ${MAIN_BRANCH}"

            // Additional steps if needed

            // Check if there are changes to commit
            def changesToCommit = sh(script: "git -C ${repository} status --porcelain", returnStatus: true) == 0

            if (changesToCommit) {
                // Commit and push the new branch to the remote repository
                sh "git -C ${repository} commit -m 'Create ${NEW_BRANCH}'"
                sh "git -C ${repository} push origin ${NEW_BRANCH}"
            } else {
                echo "No changes to commit. Skipping commit and pushing the branch."
            }
        } else {
            echo "Branch ${NEW_BRANCH} already exists locally in ${repository}. Skipping branch creation."
               def changesToCommit = sh(script: "git -C ${repository} status --porcelain", returnStatus: true) == 0
                if (changesToCommit) {
                // Commit and push the new branch to the remote repository
                sh "git -C ${repository} commit -m 'Create ${NEW_BRANCH}'"
                sh "git -C ${repository} push origin ${NEW_BRANCH}"
            } else {
                echo "No changes to commit. Skipping commit and pushing the branch."
            }
        }

        // Additional steps if needed
    }
}

