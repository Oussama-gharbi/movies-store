def imageName = 'movies-store'
def password = '*YT87az$$'
def registry = 'oussamagharbi'

pipeline{

    agent any
environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }

stages{

    stage('Build test') {
        steps{
        script{

            sh "docker build -t ${imageName}-test -f Dockerfile.test ."
        }    

}
    }
    stage('Tests'){
         parallel{
            stage('lint test'){
        steps{
               
            script{
                sh "docker run --rm ${imageName}-test npm run lint"
                sh "docker run --rm ${imageName}-test npm run test"
                 sh "docker run --rm -v $PWD/coverage:/app/coverage ${imageName}-test npm run coverage-html"
                publishHTML (target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: "$PWD/coverage",
                    reportFiles: "index.html",
                    reportName: "Coverage Report"
                ])
            }
            
        }
            }
  }
    }





  /* stage('Static Code Analysis'){
    steps {
        withSonarQubeEnv('sonar-pro') {
            sh 'sonar-scanner'
        }
    }
    }*/

    /*stage("Quality Gate"){
        steps{
        timeout(time: 5, unit: 'MINUTES') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
            }
        }
    }*/
stage('CODE ANALYSIS with SONARQUBE') {

            environment {
                scannerHome = tool 'mysonarscanner4'
            }
steps {
                withSonarQubeEnv('sonar-pro') {
                    sh '''${scannerHome}/bin/sonar-scanner  \
                    -sonar-scanner \
                    -Dsonar.projectKey=movies-store \
                    -Dsonar.sources=. \
                    
                }

                // timeout(time: 10, unit: 'MINUTES') {
                //     waitForQualityGate abortPipeline: true
                // }
            }


}



    stage('Build'){
        steps{
        script{

            sh "docker build -t ${imageName}  ."
             echo ' docker login...'
                           //   sh "docker login -u oussamagharbi -p ${password}"
                   withCredentials([usernamePassword(credentialsId: 'docker_hub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                   sh 'echo $PASS | docker login -u $USER --password-stdin'
                 }
                  echo ' Docker push...'
                  sh "docker tag movies-store oussamagharbi/movies-store:${tag}"
                  sh "docker push oussamagharbi/movies-store:${tag}"
        }  
        }  
    }

    }

}