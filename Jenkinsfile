pipeline {
    agent any
    stages {
        stage('Fetch Code'){
            steps{
                git credentialsId: '03acb4bd-b245-47bb-b870-7c10c022a0c3', url: 'git@github.com:stynhaq/nodeapp-jenkins.git'
            }
        }   
        stage('Install Dependencies'){
            steps{
                npm command: 'install', workspaceSubdirectory: 'app'
            }
        }
        stage('Test Code'){
            steps{
                npm command: 'test', workspaceSubdirectory: 'app'
            }
        }
        stage('Increment Version'){
            steps{
                npm command: 'version patch', workspaceSubdirectory: 'app'
            }
        }
        stage('Commit Code'){
            steps{
                sh "git config --local user.name stynaq"
                sh "git config --local user.email stynhaq@live.com"
                sh "git add ."
                sh "git commit -m updated_by_jenkins"
            }
        }
        stage('Push Code to Git'){
            steps{
                sshagent (credentials: ['03acb4bd-b245-47bb-b870-7c10c022a0c3']){
                    sh ("git push origin master")
                    
                }
            }
        }
        stage('Build Docker Image'){
		    steps{
                sh 'sudo docker build -t africodes/nodeapp-jenkins:$(node -p "require(\'./app/package.json\').version") .'
            }
        }
        stage('Push Image to  DockerHub'){
                steps{
		withCredentials([string(credentialsId: 'docker-hub-pass', variable: 'DockerHubPass')]) {
                sh "sudo docker login -u africodes -p ${DockerHubPass}"
		}
                sh 'sudo docker push africodes/nodeapp-jenkins:$(node -p "require(\'./app/package.json\').version")'
            }
        }


    }
}
