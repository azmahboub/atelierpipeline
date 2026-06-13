pipeline {
    agent any

    options {
        timeout(time: 10, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {
        stage('🌿 1. Récupération Git') {
            steps {
                echo "--> Synchronisation avec : https://github.com/azmahboub/jenkins-freestyle-atelier"
                // Jenkins télécharge automatiquement index.html, Dockerfile et Jenkinsfile
            }
        }

        stage('🧪 2. Phase de Test Source') {
            steps {
                echo "--> Analyse de conformité du code source..."
                sh 'grep -q "<h1>" index.html'
                echo "[SUCCESS] Fichier index.html validé avec succès."
            }
        }

        stage('🐳 3. Assemblage de l\'image Nginx') {
            steps {
                echo "--> Construction de l'image Docker de production..."
                sh 'docker build -t webapp-nginx:latest .'
            }
        }

        stage('🚀 4. Création & Lancement du Conteneur') {
            steps {
                echo "--> Déploiement du conteneur Web App..."
                sh '''
                    # 1. Supprime l'ancien conteneur s'il existe déjà pour éviter les conflits de nom
                    docker rm -f ma-webapp-container || true
                    
                    # 2. Démarre le nouveau conteneur sur le port 8086 de la machine
                    docker run -d --name ma-webapp-container -p 8086:80 webapp-nginx:latest
                '''
                echo "[SUCCESS] Conteneur 'ma-webapp-container' actif sur le port 8080 !"
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline validé avec succès !"
            mail to: 'azmahboub@gmail.com',
                 subject: "🚀 DEPLOY SUCCESS: WebApp Active [Build #${env.BUILD_NUMBER}]",
                 body: """Bonjour Aziz,

Le pipeline GitOps s'est exécuté sans aucune erreur.
L'application web Nginx a été empaquetée et le conteneur est désormais en ligne.

- Dépôt : ${env.GIT_URL}
- Build de référence : #${env.BUILD_NUMBER}
- URL Jenkins : ${env.BUILD_URL}

L'automatisation Jenkins s'est déroulée avec succès."""
        }
        failure {
            echo "🚨 Le pipeline a rencontré une erreur inattendue."
        }
    }
}
