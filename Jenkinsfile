import groovy.json.JsonSlurperClassic

def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        stage("Paso 1: Compliar"){
            steps {
                script {
                sh "echo 'Compile Code!'"
                // Run Maven on a Unix agent.
                sh "mvn clean compile -e"
                }
            }
        }
        stage("Paso 2: Testear"){
            steps {
                script {
                sh "echo 'Test Code!'"
                // Run Maven on a Unix agent.
                sh "mvn clean test -e"
                }
            }
        }
        stage("Paso 3: Build .Jar"){
            steps {
                script {
                sh "echo 'Build .Jar!'"
                // Run Maven on a Unix agent.
                sh "mvn clean package -e"
                }
            }
            post {
                //record the test results and archive the jar file.
                success {
                    archiveArtifacts artifacts:'build/*.jar'
                }
            }
        }
       stage("Paso 4: Download and checkout"){
           steps {
               checkout(
                   [$class: 'GitSCM',
                   //Acá reemplazar por el nonbre de branch
                   branches: [[name: "feature-sonar" ]],
                   //Acá reemplazar por su propio repositorio
                   userRemoteConfigs: [[url: 'https://github.com/CursoDevOpsUsach/ejemplo-maven.git']]])
           }
       }
       stage("Paso 5: Análisis SonarQube"){
           steps {
               withSonarQubeEnv('sonarqube') {
                   sh "echo 'Calling sonar Service in another docker container!'"
                   // Run Maven on a Unix agent to execute Sonar.
                   sh 'mvn clean verify -Dsonar.analysis.mode=org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.1.2184:sonar -Dsonar.projectKey= Taller-8-M3'
               }
           }
       }
    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
            sh "echo 'fase success'"
        }
        failure {
            sh "echo 'fase failure'"
        }
    }
}