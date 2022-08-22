pipeline {
    agent { label 'buildtool' }
    environment {
        jfrog_repo = "devops9989.jfrog.io"
    }
    parameters {
        choice choices: ['dev', 'stg', 'prod', 'int', 'Test'], name: 'enviroment'
        booleanParam defaultValue: true, description: 'Build image ', name: 'Build_image'
        choice choices: ['none', 'compile', 'package', 'clean package', 'clean'], name: 'GOAL'
        string defaultValue: 'devops123', description: 'jfrog repo Name', name: 'image_repo', trim: true
    }
    stages {
        /*
        stage('Git'){
            steps{
                git branch: 'main', url: 'https://github.com/repo-devops/spring-petclinic.git'
            }
        }
        stage('checkout'){
            steps{
                checkout([$class: 'GitSCM',
                    branches: [[name: main]],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/repo-devops/spring-petclinic.git']]])
            }
        }*/
        stage('Build the Code and sonarqube-analysis') {
            steps {
                when { expression { params.GOAL != 'none' } }
                //withSonarQubeEnv('sonar') here name will which integrated in jenkins configuration
                withSonarQubeEnv('sonar') {
                    sh script: "mvn ${params.GOAL} sonar:sonar"
                }
            }
        }
        stage('reporting') {
            steps {
                when { expression { params.GOAL != 'none' } }
                    junit testResults: 'target/surefire-reports/*.xml'
            }
        }
        stage('build docker image'){
          steps {
            script {
                    if (${ params.Build_image } == true) {
                    sh "echo 'started Build Image'"
                    sh 'docker image build -t ${jfrog_repo}/${params.image_repo}/${params.enviroment}/spring_petclinic:$BUILD_NUMBER .'
                    sh "echo 'image created sucessfully'"
                    }
                    else {
                    sh "echo 'image build skiped '"
                    }
                }
            }
        }
        stage('Pushing Image with Versioning') {
            steps {
                script {
                    if (${ params.Build_image } == true ) {
                        withCredentials([usernamePassword(credentialsId: 'jfrog', passwordVariable: 'jPassword', usernameVariable: 'jUser')]) {
                   sh "docker login ${jfrog_repo} -u ${env.jUser} -p ${env.jPassword}"
                   sh 'docker push ${jfrog_repo}/${params.image_repo}/${params.enviroment}/spring_petclinic:$BUILD_NUMBER'
                        }
                    }
                    else {
                sh "echo 'image is not build '"
                    }
                }
            }
        }
        stage('Delete local Images'){
            steps {
                script {
                    if (${ params.Build_image } == true ) {
                sh 'docker rmi ${jfrog_repo}/${params.image_repo}/${params.enviroment}/spring_petclinic:$BUILD_NUMBER'
                    }
                else {
                sh "echo 'no images are presented'"
                    }
                }
            }

        }
    }
}
