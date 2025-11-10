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
            // Utilise le nom du serveur configuré à l'Étape 2
            withSonarQubeEnv('SonarQube Local') { 
                steps {
                    // L'atelier demande d'utiliser la commande Maven (mvn sonar:sonar).
                    // Cela nécessite que Maven soit installé sur la machine Vagrant et configuré dans Jenkins,
                    // et que vous ayez un pom.xml minimal dans votre projet.
                    sh 'mvn sonar:sonar' 
                }
            }
        }
    }
}