pipeline {
    agent any

    environment {
        MAIN_BRANCH = 'Phase-2'
        NEW_BRANCH = 'release-3'
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

     //withCredentials([usernamePassword(credentialsId: 'githubtoken', passwordVariable: 'ACCESS_TOKEN', usernameVariable: 'USERNAME')])
    // Create a new branch in the specified repository
    sh "git checkout ${MAIN_BRANCH}"
    //sh "git pull origin ${MAIN_BRANCH}"

    // Pull with rebase to reconcile divergent branches
    sh "git pull --rebase origin ${MAIN_BRANCH}"

    // Check if the branch already exists
    def branchExists = sh(script: "git rev-parse --verify ${NEW_BRANCH}", returnStatus: true) == 0

    if (!branchExists) {
        sh "git checkout -b ${NEW_BRANCH}"
        sh "git push origin ${NEW_BRANCH}"
    } else {
        echo "Branch ${NEW_BRANCH} already exists. Skipping branch creation."
    }


    sh "git checkout -b ${NEW_BRANCH}"
    sh "git push origin ${NEW_BRANCH}"

    // Additional steps if needed
}
