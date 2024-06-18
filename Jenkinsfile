pipeline{
    agent{
        label 'amazonvm_slave'
    }
    tools{
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }
    environment {
        SCANNER_HOME = tool "sonar-server"
    }

    stages{
        stage('SCM checkout'){
            steps{
                sh 'echo cloning the repo into the slave machine'
                git branch: 'main', url: 'https://github.com/9030319796/Board-game.git'
            }
        }
        stage('compile source code'){
            steps{
                
                sh '''echo compiling the source code
                mvn compile'''
            }
        }
        stage('test compile source code'){
            steps{
                sh '''echo testing the compiled source code using suitable unit testing framework
                mvn test''' 
            }
        }
        stage('build project'){
            steps{
                sh '''echo cleaing the maven projct by deleting any existing target directory and build the project
                mvn clean package
                ls -al target'''
            }
        }
        stage('scan file system'){
            steps{
                sh '''echo scanning the files in the cloned git repository using trivy
                trivy fs --format table -o trivy-fs-report.html .'''
            }
        }
        // stage('code quality check'){
        //     steps{
        //         withSonarQubeEnv('sonar-server') {
        //             sh '''echo performing source code quality analysis using soarqube-scanner
        //             mvn sonar:sonar -Dsonar.projectName="Board-Game" -Dsonar.projectKey="Board-Game" -Dsonar.java.binaries=./target/classes'''
        //         }
        //     }
        // }
        //  stage("Sonarqube Analysis"){
        //     steps{
        //         withSonarQubeEnv('sonar-server') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=board-game-app \
        //             -Dsonar.projectKey=board-game-app \
        //             -Dsonar.userHome=`pwd`/.sonar \
        //             -Dsonar.exclusions=**/*.java
        //             '''
        //         }
        //     }
        // }
        // stage('quality gate'){
        //     steps{
        //        script{
        //            try{
        //                 timeout(time: 10, unit: 'MINUTES') {
        //                     sh "echo pipeline execution will be halted for upto to 10 minutes for receiving the quaity gate status from sonarqube server"
        //                     waitForQualityGate abortPipeline: true, credentialsId: 'sonarqube_token'
        //                 }
        //            }catch(Exception e){
        //                error 'timeout error raised because the quality gate status is not received'
        //            }
        //        }
                
        //     }
        // }
        // stage("Quality Gate"){
        //    steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
        //         }
        //     } 
        // }
        // stage('deploy the artifact to nexus'){
        //     steps{
        //         withMaven(globalMavenSettingsConfig: '', jdk: 'JAVA_HOME', maven: 'MAVEN_HOME', mavenSettingsConfig: 'nexus', traceability: true) {
        //             sh '''echo deploying the build artifact to the nexus repository with version 0.0.${BUILD_NUMBER}
        //             mvn deploy'''
        //         }
        //     }
        // }
        stage('Artifacts Upload') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'http://192.168.29.19:8081/',
                    groupId: 'com.example',
                    version: '3',
                    repository: 'local-snapshots',
                    credentialsId: 'Nexus-cred',
                    artifacts: [
                        [artifactId:'my-project',
                        classifier: '',
                        file: 'target/*.jar',
                        type: 'jar']
                  ]
              )
            }
        }
        // stage('build-scan-push docker image'){
        //     environment{
        //         image_tag="venkateshreddy679/board-game:${env.BUILD_NUMBER}"
        //     }
        //     steps{
        //         sh '''echo buildig the docker image with tag ${image_tag}
        //         docker build -t ${image_tag} .
        //         echo scanning the docker image using trivy
        //         trivy image --format table -o trivy-image-report.html ${image_tag}'''
        //         withDockerRegistry(credentialsId: 'docker_cred', url: 'https://index.docker.io/v1/') {
        //             sh '''echo pushing the image to docker
        //             docker push ${image_tag}'''
        //         }
        //     }
        // }
        // stage('update yaml file'){
        //     steps{
        //         sh '''echo updating the deployment-service.yaml file with the latest image tag
        //         sed -i "s/board-game:[0-9]*/board-game:${BUILD_NUMBER}/g" ./deployment-service.yaml'''
        //     }
        // }
       // stage('continuous delivery'){
       //      steps{
       //          withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'kuberetes_sa_token',
       //          namespace: 'board-game', restrictKubeConfigAccess: false, serverUrl: 'https://34.125.129.163:6443') {
       //              sh ''' kubectl apply -f deployment-service.yaml'''
       //          }
       //      }
       //  }
        // stage('verify delivery and audit'){
        //     steps{
        //         withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'kuberetes_sa_token',
        //         namespace: 'board-game', restrictKubeConfigAccess: false, serverUrl: 'https://34.125.129.163:6443') {
        //             sh ''' kubectl get deployment
        //             kubectl get service
        //             sudo touch kubeaudit_result.txt
        //             sudo chmod 777 kubeaudit_result.txt
        //             kubeaudit all > kubeaudit_result.txt'''
        //         }
        //     }
        // }
    }
}
