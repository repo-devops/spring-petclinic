pipeline {
    agent { label 'buildtool' }
    parameters {
        choice(name: 'GOAL', choices: ['compile', 'package', 'clean package', 'clean'])
    }
    stages {
        /*stage('Build the Code and sonarqube-analysis') {
            agent { label 'buildtool' }
            steps {
//withSonarQubeEnv('sonar') here name will which integrated in jenkins configuration
                withSonarQubeEnv('sonar') {
                    sh script: "mvn ${params.GOAL} sonar:sonar"
                }
            }
        }
        stage('reporting') {
            steps {
                junit testResults: 'target/surefire-reports/*.xml'
            }
        }*/
        stage('build docker image'){
          agent { label 'test_docker' }
          steps {
              sh "echo '$JOB_NAME'"
              sh 'docker image build -t spring-petclinic:$BUILD_NUMBER .'
            }
        }
        stage('Pushing Image to DockerHub with Versioning') {
            agent { label 'test_docker' }
            steps {
                   withCredentials([usernamePassword(credentialsId: 'jfrog', passwordVariable: 'jPassword', usernameVariable: 'jUser')]) {
                   sh "docker login -u ${env.jUser} -p ${env.jPassword}"
                   sh 'docker tag spring-petclinic:latest devops9989.jfrog.io/devops123/spring-petclinic:latest'
                   sh 'docker push devops9989.jfrog.io/devops123/spring-petclinic:latest'
            }
          }
        }
    }
}
