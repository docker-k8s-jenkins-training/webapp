def commit_id
pipeline {
    agent any
    

    stages {
        stage('Preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {                        
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage('Image Build') {
            steps {
                echo 'Building docker image.............'
                sh "docker build -t houssemtebai/webapp:'${commit_id}' ./"
                echo 'build complete'
                echo 'pushing docker image to dockerhub.............'
                sh "docker push houssemtebai/webapp:'${commit_id}'"
                echo 'push complete'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-position-simulator:release2|houssemtebai/webapp:${commit_id}|' ./manifests/workloads.yaml"
                sh "kubectl config set current-context kubernetes-admin@kubernetes"
                sh 'kubectl get all'
                sh 'kubectl apply -f ./manifests/ '
                sh 'kubectl get all'
                echo 'deployment complete'
                
            }
        }
    }
}