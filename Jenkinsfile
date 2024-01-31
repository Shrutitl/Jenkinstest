pipeline {
    agent any

    environment {
        NEW_BRANCH_NAME = 'release-1'
        //fhGITHUB_ORG = 'your-github-org' // Replace with your GitHub organization
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Use GitHub Organization Folder plugin to discover repositories
                    def repositories = discoverRepositories()
                    
                    // Create a new branch in each repository
                    repositories.each { repo ->
                        createBranch(repo)
                    }
                }
            }
        }
    }
}

def discoverRepositories() {
    // Use the GitHub Organization Folder plugin to discover repositories
    def repositories = []
    def orgFolder = findBuildFolder()
    orgFolder.items.each { item ->
        if (item.class.canonicalName == 'com.cloudbees.hudson.plugins.folder.AbstractFolder') {
            item.getItems().each { repo ->
                repositories.add(repo.fullName)
            }
        } else {
            repositories.add(item.fullName)
        }
    }
    return repositories
}

def createBranch(repository) {
    // Create a new branch in the specified repository
    checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: "https://github.com/${repository}"]]])
    sh "git checkout -b ${NEW_BRANCH_NAME}"
    sh "git push origin ${NEW_BRANCH_NAME}"
}

def findBuildFolder() {
    // Find the GitHub Organization Folder by its name
    return Jenkins.instance.getItemByFullName(env.GITHUB_ORG)
}
