def CUSTOMERS_VETS_SERVICES = []
def GENAI_VISITS_SERVICES = []

pipeline {
    agent none
    stages {
        stage('Check Changes') {
            agent { label 'ptb-agent' }
            steps {
                script {
                    def changes = sh(script: "git diff --name-only HEAD~1", returnStdout: true).trim().split("\n")

                    if (changes.any { it.startsWith("spring-petclinic-customers-service/") }) { CUSTOMERS_VETS_SERVICES.add('customers-service') }
                    if (changes.any { it.startsWith("spring-petclinic-vets-service/") }) { CUSTOMERS_VETS_SERVICES.add('vets-service') }
                    if (changes.any { it.startsWith("spring-petclinic-genai-service/") }) { GENAI_VISITS_SERVICES.add('genai-service') }
                    if (changes.any { it.startsWith("spring-petclinic-visits-service/") }) { GENAI_VISITS_SERVICES.add('visits-service') }
                }
            }
        }

        stage('Build if Customers & Vets are changed') {
            when {
                expression { return CUSTOMERS_VETS_SERVICES.size() > 0 }
            }
            agent { label 'ptb-agent' }
            steps {
                script {
                    for (service in CUSTOMERS_VETS_SERVICES) {
                        echo "Building ${service}........"
                        sh "./mvnw install -f spring-petclinic-${service}"
                        junit "spring-petclinic-${service}/target/surefire-reports/*.xml"
                    }    
                }
            }
        }

        // stage('Test if Customers & GenAI are changed') {
        //     when {
        //         expression { return env.CUSTOMERS_GENAI_SERVICES != '' }
        //     }
        //     agent { label 'nnh-agent || ptb-agent' }
        //     steps {
        //         script {
        //             def services = env.CUSTOMERS_GENAI_SERVICES.split(",")

        //             for (service in services) {
        //                 echo "Running tests for ${service}"
        //                 dir("${service}") {
        //                     sh "./mvnw test"
        //                     junit '**/target/surefire-reports/*.xml'
        //                     jacoco execPattern: '**/target/jacoco.exec',
        //                            classPattern: '**/target/classes',
        //                            sourcePattern: '**/src/main/java'
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage('Test if Vets & Visits are changed') {
        //     when {
        //         expression { return env.VETS_VISITS_SERVICES != '' }
        //     }
        //     agent { label 'nnh-agent || ptb-agent' }
        //     steps {
        //         script {
        //             def services = env.VETS_VISITS_SERVICES.split(",")

        //             for (service in services) {
        //                 echo "Running tests for ${service}"
        //                 dir("${service}") {
        //                     sh "./mvnw test"
        //                     junit '**/target/surefire-reports/*.xml'
        //                     jacoco execPattern: '**/target/jacoco.exec',
        //                            classPattern: '**/target/classes',
        //                            sourcePattern: '**/src/main/java'
        //                 }
        //             }
        //         }
        //     }
        // }

        

        // stage('Build if Vets & Visits are changed') {
        //     when {
        //         expression { return env.VETS_VISITS_SERVICES != '' }
        //     }
        //     agent { label 'nnh-agent || ptb-agent' }
        //     steps {
        //         script {
        //             def services = env.VETS_VISITS_SERVICES.split(",")

        //             for (service in services) {
        //                 echo "Building ${service}"
        //                 dir("${service}") {
        //                     sh "./mvnw clean package"
        //                 }
        //             }
        //         }
        //     }
        // }
    }
}