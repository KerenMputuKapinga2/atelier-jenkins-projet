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
                    # 1. Tenter d'arrêter et de supprimer l'ancien conteneur (si existant)
                    # '|| true' permet au pipeline de ne pas échouer si le conteneur n'existe pas.
                    echo "Arrêt de l'ancien conteneur..."
                    docker stop mini-jenkins-angular || true
                    
                    echo "Suppression de l'ancien conteneur..."
                    docker rm mini-jenkins-angular || true

                    # 2. Lancer la nouvelle image en tant que conteneur
                    # -d: Détaché (en arrière-plan)
                    # -p 8080:80: Mapper le port 8080 de l'hôte au port 80 du conteneur (Nginx)
                    # --name: Nommer le conteneur pour pouvoir le référencer facilement
                    echo "Démarrage du nouveau conteneur..."
                    docker run -d \\
                        -p 8080:80 \\
                        --name mini-jenkins-angular \\
                        kerenmputu2209/mini-jenkins-angular:1.0
                    
                    echo "Déploiement terminé. Application accessible sur le port 8080 de la machine Jenkins."
                '''
            }
        }
        
    }
}