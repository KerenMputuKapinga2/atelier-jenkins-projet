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
                    withCredentials([string(credentialsId: 'sonartoken', variable: 'SONAR_TOKEN')]) {
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
                // ATTENTION: VOTRE_ID_DOCKER par votre nom d'utilisateur Docker Hub
                sh 'docker build -t kerenmputu2209/mini-jenkins-angular:1.0 . --no-cache'
            }
        }
        
        // ... stage Docker Push ...

        
        stage('Docker Push') {
            steps {
                // 🔑 Étape d'authentification Docker Hub
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials', // ⬅️ L'ID de l'identifiant créé ci-dessus
                    usernameVariable: 'DOCKER_USERNAME', 
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    // 1. Se connecter à Docker Hub en utilisant le PAT comme mot de passe
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}" 

                    // 2. Pousser l'image
                    sh 'docker push kerenmputu2209/mini-jenkins-angular:1.0'
                }
            }
        }

        stage('Deployment') {
            steps {
                sh '''
                    # ... (arrêt et suppression de l'ancien conteneur)
                    docker stop mini-jenkins-angular || true
                    docker rm mini-jenkins-angular || true

                    # 2. Lancer la nouvelle image sur le port 8081
                    echo "Démarrage du nouveau conteneur sur le port 8081..."
                    docker run -d \\
                        -p 8081:80 \\  <-- CHANGEMENT ICI
                        --name mini-jenkins-angular \\
                        kerenmputu2209/mini-jenkins-angular:1.0
                '''
            }
        }
        
    }
}