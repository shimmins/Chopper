pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: 'main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/shimmins/Chopper.git']]])
            }
        }

        stage('Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-token', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh '''
                        git config --global user.name "${GIT_USERNAME}"
                        git config --global user.password "${GIT_PASSWORD}"
                        git config --global user.email "admin@example.com"
                        git config --global credential.helper store
                    '''
                }
            }
        }

        stage('Argo Git Clone') {
            steps {
                git branch: 'main', credentialsId: 'credintials-ssh', url: 'https://github.com/shimmins/Chopper.git'
                sh '''
                    rm -rf templates/deployment.yaml
                    sed "s/VERSIONTAG/"${TO_DO_LIST_VERSION}"/g" "templates/deployment-template.yaml" > templates/deployment.yaml
                    ls -al
                    git add --all
                    git commit -m 'update image tag'
                    eval $(ssh-agent -s)
                    ssh-add ~/.ssh/id_rsa
                    git push --set-upstream git@github.com:shimmins/Chopper.git main
                    git push
                    ssh-agent -k
                '''
            }
        }

    }
}
