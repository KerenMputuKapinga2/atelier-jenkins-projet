pipeline {
    agent any

    stages {
        stage('Verification Initiale') {
            steps {
                echo "Hello World! Le pipeline commence bien."
            }
        }

        stage('GIT Clone') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/KerenMputuKapinga2/atelier-jenkins-projet.git', 
                    credentialsId: 'github-pat-keren' 
            }
        }

        stage('Angular Build') {
            steps {
                sh 'npm install'
                sh 'npm run build --prod'
            }
        }

        stage('Archivage Artifact') {
            steps {
                archiveArtifacts artifacts: 'dist/mini-jenkins-angular/**', onlyIfSuccessful: true
            }
        }

        stage('SonarQube Analysis') { 
            withSonarQubeEnv('SonarQube Local') { 
                steps { // <-- Ce 'steps' est au mauvais niveau, il doit être au-dessus du 'withSonarQubeEnv'
                    sh 'mvn sonar:sonar' 
                }
            }
        }
    }
}