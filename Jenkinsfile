pipeline {
    agent any
    tools {
        maven 'maven_3_5_0' 
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Java-Techie-jt/devops-automation']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    sh 'docker build -t adissiva/personal-info .'
                }
            }
        }
        stage('Push image to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                        sh 'docker login -u adissiva -p dckr_pat_xk4-1P3_5R75EtnpoPU4a3Dd6QM'
                    }
                    sh 'docker push adissiva/personal-info'
                }
            }
        }
        stage('Deploy to k8s') {
            steps {
                // script{
                //     kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                // }
                //kubernetes continuous deploy plugin is deprecated
                container('kubectl') {
                    withCredentials([file()]) {
                        sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" myweb.yaml'
                        sh 'kubectl apply -f myweb.yaml'
                    }
                }
            }
        }
    }
}
