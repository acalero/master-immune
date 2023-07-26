pipeline {
    agent any

    stages {
 
        stage('Build') {
            steps {
                sh "mvn clean install -DskipTests"
            }
        }
        stage('Unit Tests') {
            steps {
                sh "mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install -Dmaven.test.failure.ignore=true"
            }
        }
        stage('Dependency Check Analysis') {
            steps {
                sh "mvn org.owasp:dependency-check-maven:check -Dformat=all"
            }
        }
        stage('Generate Software Bill of Materials (SBOM)') {
            steps {
                sh "mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom"
            }
        }
        stage('Dependency Track SBOM Upload') {
            steps {
                sh "mvn io.github.pmckeown:dependency-track-maven-plugin:upload-bom -Ddependency-track.dependencyTrackBaseUrl=http://localhost:8081 -Ddependency-track.apiKey=AloYFICJ2cZIV7ZP3Apm2UHe759mogH4"
            }
        }
        stage('Code Quality and Security Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-immune') {
                    catchError {
                        sh "mvn sonar:sonar -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.token=$SONAR_AUTH_TOKEN -Dsonar.qualitygate.wait=true"
                    }
                }
            }
        }
        stage("Quality Gate Check") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage("Deploy") {
            steps {
                echo 'Deploying to production'
            }
        }
    }
}

