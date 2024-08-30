pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
        // docker 'docker-latest'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        // DOCKERHUB_CREDENTIALS = credentials('jenkins-docker-id')
    }

    stages {
        stage('Git-Checkout') {
            steps {
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/MohammadRafi44/DevopsCICD--project-3_ultimateCICD_Corporate_DevOps_Pipeline.git']])
            }
        }
        
        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Unit-Test') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }

        stage('SONARQUBE-ANALYSIS'){
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=EKART \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=EKART '''
                }
            }
        }

        stage('Owasp-Dep-Check'){
            steps {
                    dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        // stage('Trivy-FS-Scan'){
        //     steps {
        //         // sh 'trivy fs --security-checks vuln,config /var/lib/jenkins/workspace/cicd-devops-pipiline'
        //         sh 'trivy fs --format table -o trivy-fs-report.html .'
        //     }
        // }

        stage('Mvn-Build'){
            steps {
                sh " mvn package -DskipTests=true"
            }
        }

        stage('Nexus-Publish-artifact'){
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy -DskipTests=true'
                }
                // withMaven(globalMavenSettingsConfig: 'settings-maven', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                //    sh 'mvn deploy -DskipTests=true'
                // }
            }
        }

        // stage('DOCKER-BUILD'){
        //     steps {
        //         script {
        //             withDockerRegistry(credentialsId: 'dockerhub-cred', url: 'https://index.docker.io/v1/') {
        //                 sh "docker build -t mohammadrafi44/taskmaster:latest ."
        //             }
        //         }
        //     }
        // }

        // stage('Trivy-image-scan'){
        //     steps {
        //         sh 'trivy image --format table -o image.html mohammadrafi44/taskmaster:latest'
        //         }
        // }

        // stage('Docker-Publish'){
        //     steps {
        //         script {
        //             withDockerRegistry(credentialsId: 'dockerhub-cred', url: 'https://index.docker.io/v1/') {
        //                 sh "docker push mohammadrafi44/taskmaster:latest"
        //             }
        //         }
        //     }
        // }
        // stage("Docker-Image-Cleanup"){
        //     steps {
        //         script {
        //             echo 'docker images cleanup started'
        //             sh 'docker system prune -af'
        //             echo 'docker images cleanup finished'
        //         }
        //     }
        // }
        // stage("K8s-Deploy"){
        //     steps {
        //         withKubeConfig(caCertificate: '', clusterName: 'devopsshack-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://41BFF322BA9DC0AC6E2B601200EBB29D.gr7.ap-south-1.eks.amazonaws.com') {
        //             sh 'kubectl apply -f deployment-service.yml'
        //             sleep 30 
        //         }
        //     }
        // }
        
        // stage("K8s-Verify-Deploy"){
        //     steps {
        //         withKubeConfig(caCertificate: '', clusterName: 'devopsshack-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://41BFF322BA9DC0AC6E2B601200EBB29D.gr7.ap-south-1.eks.amazonaws.com') {
        //             sh 'kubectl get pods -n webapps'
        //             sh 'kubectl get svc -n webapps'
        //         }
        //     }
        // }
    }
}