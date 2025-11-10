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
            steps {
                withSonarQubeEnv('SonarQube Local') { 
                    // Ceci récupère le secret 'sonarqube-token' et le met dans la variable SONAR_TOKEN
                    withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                        // On passe le jeton au scanner Maven (résout l'erreur Not authorized)
                        sh "mvn sonar:sonar -Dsonar.token=${SONAR_TOKEN}"
                    }
                }
            }
        }

        // ... stage SonarQube Analysis ...
        
        stage('Docker Build') {
            steps {
                // Construit l'image Docker en utilisant le Dockerfile à la racine
                // ATTENTION: Remplacez VOTRE_ID_DOCKER par votre nom d'utilisateur Docker Hub
                sh 'docker build -t VOTRE_ID_DOCKER/mini-jenkins-angular:1.0 . --no-cache'
            }
        }
        
        // ... stage Docker Push ...

        
        stage('Docker Push') {
            steps {
                // Cette étape nécessite que le compte Docker soit configuré dans Jenkins (identifiants et login).
                // Si l'atelier le demande, utilisez 'withCredentials' pour le login avant le push.
                // Sinon, le login doit être fait en amont sur la machine Jenkins (vagrant ssh).
                sh 'docker push VOTRE_ID_DOCKER/mini-jenkins-angular:1.0'
            }
        }
        
    }
}