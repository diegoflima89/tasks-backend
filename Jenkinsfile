pipeline{
    agent any  
    stages{
        stage ('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Sonar Analysis'){
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            } 
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackend -Dsonar.host.url=http://localhost:9000 -Dsonar.login=f8bbb84084934fea3d03f2509226f69969ae8202 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage ('Quality Gate'){
            steps{
                sleep(5)
                timeout(time: 2, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline:true
                }
            }
        }
        stage ('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'tomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test'){
            steps{
                dir('api-test') {
                git credentialsId: 'github_login', url: 'https://github.com/diegoflima89/tasks-api-test'
                bat 'mvn test'
                }
            }
        }
        stage ('Deploy Frontend'){
            steps{
                dir('frontend') {
                    git credentialsId: 'github_login', url: 'https://github.com/diegoflima89/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'tomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage ('Funcional Test'){
            steps{
                dir('funcional-test') {
                git credentialsId: 'github_login', url: 'https://github.com/diegoflima89/tasks-funcional-tests'
                bat 'mvn test'
                }
            }
        }
    }
}

