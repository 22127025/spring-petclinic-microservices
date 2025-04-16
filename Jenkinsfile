def SERVICES_CHANGED = []

pipeline {
    agent none
    stages {
        stage('Check Changes') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    def changes = sh(script: "git diff --name-only HEAD~1", returnStdout: true).trim().split("\n")

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

        // stage('Build and push image to Docker Hub') {
        //     when {
        //         expression { return SERVICES_CHANGED.size() > 0}
        //     }
        //     agent { label 'ptb-agent || nnh-agent' }
        //     steps {
        //         script {
        //             for (service in SERVICES_CHANGED) {
        //                 echo "Building and pushing image for ${service}....."
        //                 sh "docker build -t spring-petclinic-${service}:latest spring-petclinic-${service}"
        //                 sh "docker tag spring-petclinic-${service}:latest <your-dockerhub-username>/spring-petclinic-${service}:latest"
        //                 sh "docker push <your-dockerhub-username>/spring-petclinic-${service}:latest"
        //             }
        //         }
        //     }
        // }
    }
}
