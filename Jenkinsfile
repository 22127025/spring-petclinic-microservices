def SERVICES_CHANGED = []
def COMMIT_ID

pipeline {
    agent none
    stages {
        stage('Check Changes') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    def changes = sh(script: "git diff --name-only HEAD~1", returnStdout: true).trim().split("\n")
                    COMMIT_ID = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()

                    if (changes.any { it.startsWith("spring-petclinic-customers-service/") }) { SERVICES_CHANGED.add('customers-service') }
                    if (changes.any { it.startsWith("spring-petclinic-vets-service/") }) { SERVICES_CHANGED.add('vets-service') }
                    if (changes.any { it.startsWith("spring-petclinic-genai-service/") }) { SERVICES_CHANGED.add('genai-service') }
                    if (changes.any { it.startsWith("spring-petclinic-visits-service/") }) { SERVICES_CHANGED.add('visits-service') }
                }
            }
        }

        stage('Build') {
            when {
                expression { return SERVICES_CHANGED.size() > 0}
            }
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    for (service in SERVICES_CHANGED) {
                        echo "Building ${service}....."
                        sh "./mvnw clean package -f spring-petclinic-${service}"
                    }
                }
            }
        }

        stage('Test') {
            when {
                expression { return SERVICES_CHANGED.size() > 0}
            }
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    for (service in SERVICES_CHANGED) {
                        echo "Testing ${service}........"
                        sh "./mvnw test -f spring-petclinic-${service}"
                        if (service != 'genai-service') {
                            junit "spring-petclinic-${service}/target/surefire-reports/*.xml"
                            jacoco execPattern: "spring-petclinic-${service}/target/jacoco.exec", classPattern: "spring-petclinic-${service}/target/classes", sourcePattern: "spring-petclinic-${service}/src/main/java"
                        }
                        // jacoco execPattern: '**/target/jacoco.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
                    }
                }
            }
        }

        stage('Build and push image to Docker Hub') {
            when {
                expression { return SERVICES_CHANGED.size() > 0}
            }
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    for (service in SERVICES_CHANGED) {
                        echo "Building image for ${service}....."
                        sh "./mvnw clean install -P buildDocker -f spring-petclinic-${service}"

                        echo "Retag image for ${service}....."
                        sh "docker tag 22127025/devops-project2/spring-petclinic-${service}:latest 22127025/devops-project2:${COMMIT_ID}"

                        echo "Pushing image to DockerHub for ${service}....."
                        withDockerRegistry(credentialsId: 'dockerhub-token', url: 'https://index.docker.io/v1/') {
                            sh "docker push 22127025/devops-project2:${COMMIT_ID}"
                        }
                    }
                }
            }
        }
    }
}
