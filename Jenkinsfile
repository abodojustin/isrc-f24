pipeline {
    agent {
        // Utiliser un agent Docker avec Docker préinstallé
        docker { 
            image 'docker:latest'
            args '--privileged -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        IMAGE_NAME = "my-nginx-image"  // Nom de l'image Docker
        CONTAINER_NAME = "my-nginx"   // Nom du conteneur
        PORT = "9000"                 // Port d'exposition
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Clonage du dépôt contenant le projet...'
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Construction de l’image Docker...'
                sh """
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
            echo "Le conteneur est en cours d'exécution sur http://<server-ip>:${PORT}"
        }
        failure {
            echo "Le pipeline a échoué. Vérifiez les logs pour plus d'informations."
        }
    }
}
