pipeline {
    agent none

    stages {
        stage('Clone Repository') {
            agent { label 'docker-build' }
            steps {
                git branch: 'main', url: 'https://github.com/hit-ops/jenkins-master-slave.git'
            }
        }

        stage('Build Docker Image') {
            agent { label 'docker-build' }
            steps {
                script {
                    sh 'sudo docker build -t vimaldaga:v1 .'  // Corrected shell command
                }
            }
        }
        stage("push the images in dockerhub"){
            agent{label 'docker-build'}
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]){
                    script{
                        sh ''' 
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        sudo docker tag vimaldaga:v1 docker.io/hitaishi7/myapp:v1
                        sudo docker push docker.io/hitaishi7/myapp:v1
                        '''
                    }
                }             
                
            }
        }
        stage("deploy the application"){
            agent {label 'docker-prod'}
            steps{
                script{
                    sh ''' 
                    sudo docker pull hitaishi7/myapp:v1
                    sudo docker rm -f myos1 || true   
                    sudo docker run -dit --name myos1 -p 80:80 hitaishi7/myapp:v1
                    '''
                }
            }
        }
    }
}
