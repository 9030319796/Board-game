pipeline {
    agent any
    
    tools{
        jdk  'JAVA_HOME'
        maven  'MAVEN_HOME'
    }
    
    // environment {
    //     SCANNER_HOME= tool 'sonar-server'
    // }

    stages {
        // stage('Workspace Cleaning'){
        //     steps{
        //         cleanWs()
        //     }
        // }
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/9030319796/Board-game.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Unit Tests') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        
        // stage("Sonarqube Analysis"){
        //     steps{
        //         withSonarQubeEnv('sonar-server') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BOARD-GAME \
        //             -Dsonar.projectKey=BOARD-GAME \
        //             -Dsonar.exclusions=**/*.java
        //             '''
        //         }
        //     }
        // }
        // stage("Quality Gate"){
        //    steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
        //         }
        //     } 
        // }
        
        // stage('OWASP Dependency Check') {
        //     steps {
        //         dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'owasp-dp-check'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }
        // stage('TRIVY FS SCAN') {
        //     steps {
        //         sh "trivy fs . > trivyfs.txt"
        //     }
        // }
        
        stage('Build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }
        
        stage('Deploy To Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'JAVA_HOME', maven: 'MAVEN_HOME', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy -DskipTests=true"
                }
            }
        }
        
        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'DockerHubPass', toolName: 'docker') {
                        sh "docker build -t 9030319796/board-game -f Dockerfile ."
                        
                        
                    }
                }
            }
        }
        stage('Containerize And Test') {
            steps {
                script{
                    sh 'docker run -d --name board-game-app 9030319796/board-game:latest && sleep 10 && docker stop board-game-app'
                }
            }
        }
        
        stage('Trivy Scan') {
            steps {
                sh "trivy image 9030319796/board-game:latest > trivy-report.txt "
                
            }
        }
        
        stage('Push The Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'DockerHubPass', toolName: 'docker') {
                        sh "docker push 9030319796/board-game:latest"
                    }
                }
                 
            }
        }
        
        stage('Kubernetes Deploy') {
            steps {
                script{
                   
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                            sh "kubectl apply -f deployment-service.yml"
                            sh "kubectl get svc"
                        }
    
                
                    
                }
            }
        }
        
        
    }
}
    
        
    
    
