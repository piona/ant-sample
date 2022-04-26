pipeline {
  agent any
//   triggers {
//     pullRequestReview(reviewStates: ['approved'])
//   }  
  stages {
    stage('git-checkout') {
      steps {
        echo "checking out....."
        // git 'https://github.com/piona/ant-sample.git'
        git branch: '*', credentialsId: 'mahima-repo', url: 'https://github.com/Mahima-Sahu/ant-sample.git'
      }
    }    
    // stage('Build dependencies') {
    //   steps {
    //     echo "building jars....."
    //     sh """
    //       javac servicestools/sailpoint/services/tools/ant/BuildInitTask.java servicestools/sailpoint/services/tools/ant/ConditionGreaterThan.java servicestools/sailpoint/services/tools/ant/OrderedFileListing.java servicestools/sailpoint/services/tools/ant/ReadFromConsole.java servicestools/sailpoint/services/tools/ant/Validations.java
    //       jar -cf lib/services-tools.jar *.class                   
    //     """
    //   }
    // }    
    stage('Build war') {
      steps {
        echo "building war....."
        sh """
          export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
          export SPTARGET=jenkins
          export HOSTNAME=jenkins
          ant -p
          ant war
        """
      }
    }
    stage('Deploy application') {
      when {
        branch 'master'
      }      
      steps {
        echo "deploying app in tomcat server....."
        sshagent(['deploy_user']) {
          sh 'scp -v -o StrictHostKeyChecking=no build/deploy/identityiq.war ec2-user@15.236.239.176:/opt/tomcat/webapps'
        }        
      }
    }

    // stage('Sending Slack message ... ') {
    //   steps {
    //             script {
    //                 if ("${currentBuild.result}" == "SUCCESS") {
    //                     stage ('on Success') {
    //                       slackSend color: "#3DFF33", channel: "#cicd-deployment", message: "${currentBuild.result} | Job: ${env.JOB_NAME} with build no.: ${env.BUILD_NUMBER}  is ${currentBuild.result} the results of this build can be found at ${env.BUILD_URL}"
    //                     }
    //                 } else {
    //                     stage ('on Failure') {
    //                         slackSend color: "#FF5733", channel: "#cicd-deployment", message: "${currentBuild.result} | Job: ${env.JOB_NAME} with build no.: ${env.BUILD_NUMBER}  is ${currentBuild.result} the results of this build can be found at ${env.BUILD_URL}"
    //                     }
    //                 }
    //             }
    //     }        
    // }    
 
    // stage('Deploy application') { 
    //   when {
    //     branch 'master'
    //   }
    //   steps {
    //     echo "deploying app....."  
    //     sshagent(credentials: ['windows-cred']) {
    //       sh 'sshpass -p ";p@Vb3&CxRUTml(sAH7aRPVhE3WEfdl*" scp **/jar/*.jar Administrator@54.159.119.218:C:/'
    //     }
    //   }
    // }    
  }
  post {
    success {
        slackSend color: "#3DFF33", channel: "#cicd-deployment", message: "${currentBuild.result} | Job: ${env.JOB_NAME} with build no.: ${env.BUILD_NUMBER}  is ${currentBuild.result} the results of this build can be found at ${env.BUILD_URL}"
    }
    failure {
        slackSend color: "#FF5733", channel: "#cicd-deployment", message: "${currentBuild.result} | Job: ${env.JOB_NAME} with build no.: ${env.BUILD_NUMBER}  is ${currentBuild.result} the results of this build can be found at ${env.BUILD_URL}"
    } 
  }     
}
