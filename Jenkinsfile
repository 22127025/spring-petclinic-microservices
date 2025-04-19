pipeline {
    agent none
    stages {
        stage('Checkout branch') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    BRANCH_NAME = params.BRANCH_NAME ?: env.GIT_BRANCH
                    SERVICE = BRANCH_NAME.replaceAll("^origin/", "")
                    checkout([$class: 'GitSCM', branches: [[name: "${BRANCH_NAME}"]], userRemoteConfigs: [[url: 'https://github.com/22127025/spring-petclinic-microservices.git']]])
                }
            }
        }

        stage('Get Latest Commit ID') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    LATEST_COMMIT_ID = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                }
            }
        }

        stage('Build service') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    echo "Building ${SERVICE}....."
                    sh "./mvnw clean package -f spring-petclinic-${SERVICE}"
                }
            }
        }

        stage('Test service') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    echo "Testing ${SERVICE}........"
                    sh "./mvnw test -f spring-petclinic-${SERVICE}"
                    if (SERVICE != 'genai-service') {
                        junit "spring-petclinic-${SERVICE}/target/surefire-reports/*.xml"
                        jacoco execPattern: "spring-petclinic-${SERVICE}/target/jacoco.exec", classPattern: "spring-petclinic-${SERVICE}/target/classes", sourcePattern: "spring-petclinic-${SERVICE}/src/main/java"
                    }
                }
            }
        }

        stage('Build and push image to Docker Hub') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    echo "Building image for ${SERVICE}....."
                    sh "./mvnw clean install -P buildDocker -f spring-petclinic-${SERVICE}"

                    echo "Retag image for ${SERVICE}....."
                    sh "docker tag 22127025/devops-project2/spring-petclinic-${SERVICE}:latest 22127025/devops-project2:${LATEST_COMMIT_ID}"

                    echo "Pushing image to DockerHub for ${SERVICE}....."
                    withDockerRegistry(credentialsId: 'dockerhub-token', url: 'https://index.docker.io/v1/') {
                        sh "docker push 22127025/devops-project2:${LATEST_COMMIT_ID}"
                    }
                }
            }
        }


        /////////////////////////////////////////////////////////////////////////////////////////////////
        /////////////////////////////////////////////////////////////////////////////////////////////////





                // stage('Check Changes') {
        //     agent { label 'ptb-agent || nnh-agent' }
        //     steps {
        //         script {
        //             def changes = sh(script: "git diff --name-only HEAD~1", returnStdout: true).trim().split("\n")
        //             COMMIT_ID = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()

        //             if (changes.any { it.startsWith("spring-petclinic-customers-service/") }) { SERVICES_CHANGED.add('customers-service') }
        //             if (changes.any { it.startsWith("spring-petclinic-vets-service/") }) { SERVICES_CHANGED.add('vets-service') }
        //             if (changes.any { it.startsWith("spring-petclinic-genai-service/") }) { SERVICES_CHANGED.add('genai-service') }
        //             if (changes.any { it.startsWith("spring-petclinic-visits-service/") }) { SERVICES_CHANGED.add('visits-service') }
        //         }
        //     }
        // }

        // stage('Build') {
        //     when {
        //         expression { return SERVICES_CHANGED.size() > 0}
        //     }
        //     agent { label 'ptb-agent || nnh-agent' }
        //     steps {
        //         script {
        //             for (service in SERVICES_CHANGED) {
        //                 echo "Building ${service}....."
        //                 sh "./mvnw clean package -f spring-petclinic-${service}"
        //             }
        //         }
        //     }
        // }

        // stage('Test') {
        //     when {
        //         expression { return SERVICES_CHANGED.size() > 0}
        //     }
        //     agent { label 'ptb-agent || nnh-agent' }
        //     steps {
        //         script {
        //             for (service in SERVICES_CHANGED) {
        //                 echo "Testing ${service}........"
        //                 sh "./mvnw test -f spring-petclinic-${service}"
        //                 if (service != 'genai-service') {
        //                     junit "spring-petclinic-${service}/target/surefire-reports/*.xml"
        //                     jacoco execPattern: "spring-petclinic-${service}/target/jacoco.exec", classPattern: "spring-petclinic-${service}/target/classes", sourcePattern: "spring-petclinic-${service}/src/main/java"
        //                 }
        //                 // jacoco execPattern: '**/target/jacoco.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
        //             }
        //         }
        //     }
        // }

        // stage('Build and push image to Docker Hub') {
        //     when {
        //         expression { return SERVICES_CHANGED.size() > 0}
        //     }
        //     agent { label 'ptb-agent || nnh-agent' }
        //     steps {
        //         script {
        //             for (service in SERVICES_CHANGED) {
        //                 echo "Building image for ${service}....."
        //                 sh "./mvnw clean install -P buildDocker -f spring-petclinic-${service}"

        //                 echo "Retag image for ${service}....."
        //                 sh "docker tag 22127025/devops-project2/spring-petclinic-${service}:latest 22127025/devops-project2:${COMMIT_ID}"

        //                 echo "Pushing image to DockerHub for ${service}....."
        //                 withDockerRegistry(credentialsId: 'dockerhub-token', url: 'https://index.docker.io/v1/') {
        //                     sh "docker push 22127025/devops-project2:${COMMIT_ID}"
        //                 }
        //             }
        //         }
        //     }
        // }
    }
}
