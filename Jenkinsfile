 
pipeline {
    agent any 
    parameters {
        choice(name: 'GOAL', choices: ['compile', 'package', 'clean package'])
    }
    stages {
        stage('Build') {
            steps {
//withSonarQubeEnv('sonar') here name will which integrated in jenkins configuration
                //withSonarQubeEnv('sonar') {
                    sh "mvn ${params.GOAL}"
                }
            }
        }*/
        stage('reporting') {
            steps {
                junit testResults: 'target/surefire-reports/*.xml'
            }
        }
    }
}
