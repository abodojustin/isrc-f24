pipeline {
    agent {
        docker { 
            image 'docker:latest'
            args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        IMAGE_NAME = "my-nginx-image"  // Nom de l'image Docker
        CONTAINER_NAME = "my-nginx"   // Nom du conteneur
        PORT = "8080"                 // Port d'exposition
        IP_ADDRESS = "192.168.1.1"
        GIT_REPO = "https://github.com/username/web-app.git" // Remplacez par l'URL de votre dépôt
        GIT_BRANCH = "main"           // Branche Git à cloner
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Clonage du dépôt contenant le projet...'
                sh """
                # Supprimer tout ancien dossier existant
                rm -rf web-app || true
                
                # Cloner le dépôt depuis Git
                git clone --branch ${GIT_BRANCH} ${GIT_REPO} web-app
                """
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Construction de l’image Docker...'
                sh """
                cd web-app
                docker build -t ${IMAGE_NAME} .
                """
            }
        }
        stage('Run Docker Container') {
            steps {
                echo 'Exécution du conteneur Docker...'
                sh """
                # Arrêter et supprimer le conteneur existant s'il existe
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                
                # Lancer un nouveau conteneur
                docker run -d -p ${PORT}:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}
                """
            }
        }
    }
    post {
        success {
            echo "Le conteneur est en cours d'exécution sur http://${IP_ADDRESS}:${PORT}"
        }
        failure {
            echo "Le pipeline a échoué. Vérifiez les logs pour plus d'informations."
        }
    }
}
