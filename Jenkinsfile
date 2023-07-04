pipeline {
    agent any

     environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "ap-south-1"
    }
    
    // tools {
    //     jdk 'jdk11'
    //     maven 'maven3'
    // }
    
    // environment {
    //     SCANNER_HOME= tool 'sonar-scanner'
    // }


    stages {
        stage('Git checkout') {
            steps {
                 git branch: 'main', changelog: false, poll: false, url: 'https://github.com/bhavanishankar26/shopping-cart-shankar.git'
            }
        }
        
    //     stage('Compile') {
    //         steps {
    //             sh "mvn clean compile"
                
    //         }
    //     }
        
    //     stage('Sonarqube Analysis') {
    //         steps {
    //                     sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://http://3.111.54.197:9000/ -Dsonar.login=squ_da8d546d3320e2faeeef69d2f5e259e21075a86c -Dsonar.projectName=shopping-cart \
    //         -Dsonar.java.binaries=. \
    //         -Dsonar.projectKey=shopping-cart'''
    //               }
    //     }
        
    //     stage('OWASP Scan') {
    //         steps {
    //             dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
    //             dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
    //         }
    //     }
        
    //       stage('Build') {
    //         steps {
    //             sh "mvn clean package -DskipTests=true"
    //         }
    //     }
        
    //     stage('Docker Build & Push') {
    // steps {
    //     script {
    //         withDockerRegistry(credentialsId: 'f0d72c9f-c017-41dd-8222-9dcfe88f669f', toolName: 'docker') {
    //             sh "docker build -t shopping-cart:latest -f docker/Dockerfile ."
    //             sh "docker tag shopping-cart:latest gadebhavani26/shopping-cart:latest"
    //             sh "docker push gadebhavani26/shopping-cart:latest"
    //         }
    //     }
    // }
    //     }

          stage("Deploy to EKS") {
            steps {
                script {
                    dir('.') {
                        sh "aws eks --region ap-south-1 update-kubeconfig --name terraform-eks-demo"
                        sh "sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.23.6/bin/linux/amd64/kubectl"
                        sh "sudo chmod +x ./kubectl"
                        sh "sudo mkdir -p $HOME/bin && sudo cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin"
                        sh "kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.0/deploy/static/provider/cloud/deploy.yaml"
                        sh "kubectl apply -f deploymentservice.yml"
                    }
                }
            }
        }

          // stage ("Kube Deploy") {
          //   steps {
          //       withKubeConfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1EY3dNekF6TXprME5Gb1hEVE16TURZek1EQXpNemswTkZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTWpBCnlRN1hmTjdlNDgwbndaK3dQdVhweFF2RDdCSXpUVUprSE9VVTdTVCtFYVZHcFJ2YkFGTDNYRUx4Y3A0S2JVVWQKWFIzeXlaVnVVanlCTUpJWXRHQ0VoMWt1NzBHMXJPVm9DSjIrcTBURHd1UzI1VURqdlR2d1NtMHk4ZmVLS0N1RgpRYmlRc3FrbW5tdlJoaitsZGh3NlVMU3U5WWxFSEkrZG5KUWk5dVY5Q0ovUHZLRlVTcG8xK3p0SUcrdDEvSStzCkp1OGsrNUdQZ093T2pLZzJLTjFGRktYeGYySFZPS01yQkFNclBpTzJNYnJkYTRRek1IdUExTGc2b0lmeFZNUXcKdzgwVkNLbTExcGNoVEFkL2UrdXNuRmp3WFk4VTdoSlRvek1Wc3VUQjRXaEY3ZFNvQVViZmpLRnpjY1d2VEpaVgpTaVMzcnZwMGJ6NDArNTdwaWNrQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZBdFllWTlrNjZRUlMybnRSdkNKMUQ5bmhUY1VNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBR0NiZ1VrL1Vib0JhOFVuZHJsRApKdzJoWTl3M1JPV1FpVkQxeWoxYlBCQVZ0aUp5ZzlmQ3c0M0QwRXMxclR2SmZRaytRWnJ2a04yRWN0bndMQlVwCk9xZ1JDWUFncXY2NzZOZ1ZaS0dVYm9PUEN5c3RnVlNqZ3hyR0Y1SUVsM3J1YlM1cEV6Q2ptQ2pIVElpNzRtelAKbk1QemtUVHF0Z1ZyUHRMY09kUi9DdHA2VCtkZzY4QXdDbzVCVEpUVzIwbi9qb0ZvbW9qZ25sMElXdjc1YnptSAprMW1QTWRTLzYrdDNPZmhTRGxvRUNjbndtQk1yQnpVaXpZZzlNemhxMmtUc25KYzdHVG9vZTZocllTcklXM0x4CmJ3UE1WRGxxLzJiSENGV0U1TERuSFdFYWpsdm44cmdQWWtaaTRiSmptY2IxNzZISFM0OTdUYVI0TTYxL2M3TmEKSU00PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', clusterName: 'terraform-eks-demo', contextName: 'arn:aws:eks:ap-south-1:471581286766:cluster/terraform-eks-demo', credentialsId: 'Kubernetes', namespace: 'default', restrictKubeConfigAccess: false, serverUrl: 'https://D1078F00B950512D183E77A4F71FE8C6.gr7.ap-south-1.eks.amazonaws.com') {
          //       sh "kubectl apply -f deploymentservice.yml"
                }
                // withKubeConfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMvakNDQWVhZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJek1EY3dNekF6TXprME5Gb1hEVE16TURZek1EQXpNemswTkZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTWpBCnlRN1hmTjdlNDgwbndaK3dQdVhweFF2RDdCSXpUVUprSE9VVTdTVCtFYVZHcFJ2YkFGTDNYRUx4Y3A0S2JVVWQKWFIzeXlaVnVVanlCTUpJWXRHQ0VoMWt1NzBHMXJPVm9DSjIrcTBURHd1UzI1VURqdlR2d1NtMHk4ZmVLS0N1RgpRYmlRc3FrbW5tdlJoaitsZGh3NlVMU3U5WWxFSEkrZG5KUWk5dVY5Q0ovUHZLRlVTcG8xK3p0SUcrdDEvSStzCkp1OGsrNUdQZ093T2pLZzJLTjFGRktYeGYySFZPS01yQkFNclBpTzJNYnJkYTRRek1IdUExTGc2b0lmeFZNUXcKdzgwVkNLbTExcGNoVEFkL2UrdXNuRmp3WFk4VTdoSlRvek1Wc3VUQjRXaEY3ZFNvQVViZmpLRnpjY1d2VEpaVgpTaVMzcnZwMGJ6NDArNTdwaWNrQ0F3RUFBYU5aTUZjd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZBdFllWTlrNjZRUlMybnRSdkNKMUQ5bmhUY1VNQlVHQTFVZEVRUU8KTUF5Q0NtdDFZbVZ5Ym1WMFpYTXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBR0NiZ1VrL1Vib0JhOFVuZHJsRApKdzJoWTl3M1JPV1FpVkQxeWoxYlBCQVZ0aUp5ZzlmQ3c0M0QwRXMxclR2SmZRaytRWnJ2a04yRWN0bndMQlVwCk9xZ1JDWUFncXY2NzZOZ1ZaS0dVYm9PUEN5c3RnVlNqZ3hyR0Y1SUVsM3J1YlM1cEV6Q2ptQ2pIVElpNzRtelAKbk1QemtUVHF0Z1ZyUHRMY09kUi9DdHA2VCtkZzY4QXdDbzVCVEpUVzIwbi9qb0ZvbW9qZ25sMElXdjc1YnptSAprMW1QTWRTLzYrdDNPZmhTRGxvRUNjbndtQk1yQnpVaXpZZzlNemhxMmtUc25KYzdHVG9vZTZocllTcklXM0x4CmJ3UE1WRGxxLzJiSENGV0U1TERuSFdFYWpsdm44cmdQWWtaaTRiSmptY2IxNzZISFM0OTdUYVI0TTYxL2M3TmEKSU00PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', clusterName: 'terraform-eks-demo', contextName: 'arn:aws:eks:ap-south-1:471581286766:cluster/terraform-eks-demo', credentialsId: 'Kubernetes', namespace: 'default', serverUrl: 'https://D1078F00B950512D183E77A4F71FE8C6.gr7.ap-south-1.eks.amazonaws.com') {
                //  sh "kubectl apply -f deploymentservice.yml"
                // }
        //     }
        // }
        
      //   stage('Deploying App to Kubernetes') {
      //    steps {
      //      script {
      //       kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "Kubernetes")
      //       sh "aws eks --region ap-south-1 update-kubeconfig --name terraform-eks-demo"
      //       sh "kubectl apply -f deploymentservice.yml"
      //      }
      //   }
      // }
        
    }
