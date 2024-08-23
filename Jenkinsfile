pipeline {
    agent any
    environment {
        DOCKERFILE_WEB = 'Dockerfileweb'       // Nome do Dockerfile para a imagem web
        DOCKERFILE_DB = 'Dockerfiledb'         // Nome do Dockerfile para a imagem db
        DOCKERFILE_NGINX = 'Dockerfilenginx'   // Nome do Dockerfile para a imagem nginx
        DOCKER_IMAGE_TAG = 'latest'            // Tag da imagem Docker a ser construída
    }
    stages {

        stage('Build Images') {
            steps {
                script {
                    // Nome das imagens
                    def imageNameWeb = 'rogerramossilva/web'
                    def imageNameDB = 'rogerramossilva/db'
                    def imageNameNginx = 'rogerramossilva/nginx'

                    // Executa o build das imagens com os respectivos Dockerfiles
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhubrogerio') {
                        def webImage = docker.build("${imageNameWeb}:${DOCKER_IMAGE_TAG}", "-f ${DOCKERFILE_WEB} .")
                        def dbImage = docker.build("${imageNameDB}:${DOCKER_IMAGE_TAG}", "-f ${DOCKERFILE_DB} .")
                        def nginxImage = docker.build("${imageNameNginx}:${DOCKER_IMAGE_TAG}", "-f ${DOCKERFILE_NGINX} .")

                        webImage.push()
                        dbImage.push()
                        nginxImage.push()
                    }
                }
            }
        }


	 stage('Teste Aplicação') {
            steps {
                // Testes automatizados da aplicação
                sh 'docker-compose -f docker-compose.yml down'
                sh 'docker-compose -f docker-compose.yml up -d'

                // Realize os testes nos serviços em execução
                // Exemplo de teste usando curl para verificar se o serviço web está respondendo
                sh 'curl -I http://localhost:80'

                sh 'docker-compose -f docker-compose.yml down'
            }
        }
        stage('Aprovação') {
            steps {
                input 'Deseja prosseguir com o Deploy e Push?'
            }
        }

   }
}
