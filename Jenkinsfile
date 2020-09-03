pipeline {

  environment {
   
     registry = "10.128.0.12:5000/syedhajirali"     
     dockerImage = ""
  }

  agent any
    stages {
  
    stage('Checkout Source') {
      steps {
        git 'https://github.com/syedhajirali/studentmaster.git'
      }
    }

     
 stage('Maven Install') {
      agent {
        docker {
          image 'maven:3.5.0'
        }
      }
      steps {
        sh 'mvn clean install'
        def pom = readMavenPom file:'pom.xml'
            print pom.version
            env.version = pom.version
      }
    } 
      
    stage('Build image') {
      steps{
        script {
           def app = docker.build "10.128.0.12:5000/studentmaster:${env.version}"
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
 
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "student.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }
   stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }

}
