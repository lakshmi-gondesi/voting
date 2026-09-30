pipeline {
    agent any

    stages {
        stage('Download') {
            steps {
                git 'https://github.com/lakshmi-gondesi/voting.git'
            }
        }

        stage('Build docker images') {
            steps {
                dir('vote') {
                    sh 'docker build -t lakshmigondesi/votingapp .'
                }
                dir('result') {
                    sh 'docker build -t lakshmigondesi/resultapp .'
                }
                dir('worker') {
                    sh 'docker build -t lakshmigondesi/workerapp .'
                }
            }
        }

        stage('Push docker images') {
            steps {
                sh 'docker push lakshmigondesi/votingapp'
                sh 'docker push lakshmigondesi/workerapp'
                sh 'docker push lakshmigondesi/resultapp'
            }
        }

        stage('Deploy to qa servers as containers') {
            steps {
                sh 'ssh ubuntu@172.31.37.185 ansible-playbook project.yml -b'
            }
        }

        stage('Download and run selenium scripts') {
            steps {
                dir('testing') {
                    git 'https://github.com/IntelliqDevops/FunctionalTesting.git'
                    sh 'java -jar testing.jar'
                }
            }
        }

        stage('Deploy into k8s cluster') {
            steps {
                sh 'ssh ec2-user@172.31.44.135 kubectl apply -f voting/k8s-specifications/'
            }
        }
    }
}
