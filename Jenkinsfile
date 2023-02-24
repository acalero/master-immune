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
                sh "mvn org.jacoco:jacoco-maven-plugin:report"
            }
        }
        stage('Dependency Check Analysis') {
            steps {
                sh "mvn org.owasp:dependency-check-maven:check -Dformat=all"
            }
        }
        stage('Dependency Track Upload') {
            steps {
                sh "mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom"
                sh "mvn io.github.pmckeown:dependency-track-maven-plugin:upload-bom -Ddependency-track.dependencyTrackBaseUrl=http://localhost:8081 -Ddependency-track.apiKey=E2M62M9kJN4tkaUphey4UWSp31p30aky"
            }
        }
        stage('Code Quality and Security Analysis') {
            steps {
                withSonarQubeEnv('sonar-playground') {
                    catchError {
                        sh "mvn sonar:sonar -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.login=$SONAR_AUTH_TOKEN"
                    }
                }
            }
        }
        stage("Deploy") {
            steps {
                echo 'deploy to production'
            }
        }
    }
}

