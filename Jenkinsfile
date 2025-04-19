// def SERVICES_CHANGED = []
// def COMMIT_ID

pipeline {
    agent none

    environment {
        GIT_REPO = 'https://github.com/22127025/spring-petclinic-microservices.git' // Your GitHub repository URL
        CREDENTIALS_ID = 'github-token' // Replace with your GitHub credentials ID in Jenkins
    }

    stages {
        stage('Checkout') {
            agent { label 'ptb-agent || nnh-agent' }
            steps {
                script {
                    // Dynamically get the branch name from the webhook payload
                    def branchName = env.GIT_BRANCH // `GIT_BRANCH` is set automatically in Jenkins for GitHub webhook triggers
                    echo "Branch name is: ${branchName}"

                    // Checkout the branch that triggered the webhook
                    git credentialsId: "${CREDENTIALS_ID}",
                        url: "${GIT_REPO}",
                        branch: branchName
                }
            }
        }

        // stage('Get Latest Commit') {
        //     agent { label 'ptb-agent || nnh-agent' }
        //     steps {
        //         script {
        //             // Get the latest commit hash
        //             LATEST_COMMIT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        //             echo "Latest Commit Hash: ${LATEST_COMMIT}"
        //         }
        //     }
        // }

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
