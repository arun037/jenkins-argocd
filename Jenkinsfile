pipeline
    agent any

    environment {
        DOCKER_REGISTRY     = 'docker.io'
        DOCKER_REPOSITORY   = 'arunagri03'
        IMAGE_NAME          = 'argocd'
        DOCKER_IMAGE_TAG    = "${DOCKER_REGISTRY}/${DOCKER_REPOSITORY}/${IMAGE_NAME}:latest"
        DOCKER_PASSWORD     = credentials('docker_token')
        SONAR_URL           = "http://3.88.237.77:9000"
    }

    tools{
        jdk 'JDK 11'
        maven 'Maven 3.8.1'
    }

    stages{
        stage('validating git hub'){
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github_key', url: 'https://github.com/arun037/jenkins-argocd.git']])

                }

            }
        }
    }

        stage('build with maven'){
            steps{
                sh 'mvn clean package'
            }
        }

        stage('sonarqube test'){
            steps{
                 withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
                  sh 'mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
            }
        }

        stage('build & push docker image'){
            script{
                sh '''
                    echo "$DOCKER_PASSWORD | docker login ${DOCKER_REGISTRY} --username arunagri03 --password-stdin
                    docker build -t ${DOCKER_IMAGE_TAG}
                    docker push ${DOCKER_IMAGE_TAG}
                '''
            }
        }