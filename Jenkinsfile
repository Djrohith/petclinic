#!groovy​

// FULL_BUILD -> true/false build parameter to define if we need to run the entire stack for lab purpose only
final FULL_BUILD = true
// HOST_PROVISION -> server to run ansible based on provision/inventory.ini
//final HOST_PROVISION = params.HOST_PROVISION
// limit: 'app_server' injecting by hardcoded
final HOST_PROVISION = '10.0.1.21'
 


final GIT_URL = 'https://github.com/Djrohith/petclinic.git'
final NEXUS_URL = '10.0.1.20:8081'

stage('Build') {
    node {
        git GIT_URL
        withEnv(["PATH+MAVEN=${tool 'm3'}/bin"]) {
            if(FULL_BUILD) {
                mvnHome = tool 'm3'
                sh "echo 'inside build'"
                
                 if (isUnix()) {
                     sh "mvn -B versions:set -DnewVersion=petclinic${BUILD_NUMBER}"
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
                
            }
        }
    }
}

if(FULL_BUILD) {
    stage('Unit Tests') {   
        node {
            withEnv(["PATH+MAVEN=${tool 'm3'}/bin"]) {
                sh "mvn -B clean test"
                stash name: "unit_tests", includes: "target/surefire-reports/**"
            }
        }
    }
}

if(FULL_BUILD) {
    stage('Integration Tests') {
        node {
            withEnv(["PATH+MAVEN=${tool 'm3'}/bin"]) {
                sh "mvn -B clean verify -Dsurefire.skip=true"
            }
        }
    }
}

if(FULL_BUILD) {
    stage('Static Analysis') {
        node {
            withEnv(["PATH+MAVEN=${tool 'm3'}/bin"]) {
                withSonarQubeEnv('sonar'){
                    unstash 'unit_tests'
                    sh 'mvn sonar:sonar -DskipTests'
                }
            }
        }
    }
}
/**
if(FULL_BUILD) {
    stage('Approval') {
        timeout(time:3, unit:'DAYS') {
            input 'Do I have your approval for deployment?'
        }
    }
}**/

if(FULL_BUILD) {
    stage('Artifact Upload') {
        node {

//            def pom = readMavenPom file: 'pom.xml'
//            def file = "${pom.artifactId}-${pom.version}"
              def jar = "target/petclinic.war"

 //           sh "cp pom.xml ${file}.pom"

            nexusArtifactUploader artifacts: [
                    [artifactId: "petclinic", classifier: '', file: "target/petclinic.war", type: 'war'],
 //                   [artifactId: "${pom.artifactId}", classifier: '', file: "petclinic.pom", type: 'pom']
                ], 
                credentialsId: '58bb8d11-4d61-4573-bbf5-c37221ff5754', 
                groupId: "com.cg", 
                nexusUrl: NEXUS_URL, 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'petclinic',
                version: "petclinic${BUILD_NUMBER}"
               
             }
       }
 }

//if(FULL_BUILD) {
//    stage('Artifact Upload') {
//       node {
           
            
//            nexusArtifactUploader artifacts: [[groupId: 'petclinic', 
//                                               artifactId: 'petclinic', classifier: '',
//                                               file: 'target/petclinic.war', type: 'war']],
//                credentialsId: '47bf81a6-31d4-41f6-8152-ad402379c823', 
//                groupId: 'br.com.meetup.ansiblea75e53f8-48ab-4c25-84b2-dfcb7981148b', nexusUrl: "${NEXUS_URL}", nexusVersion: 'nexus2', 
//                protocol: 'http', repository: 'demoapp-rele'

             
//        }
//    }
//}
//
//
stage('Deploy') {
    node {
        
        

       // http://54.70.187.156:8081/repository/demoapp-rele/br/com/meetup/ansible/soccer-stats/0.0.2-3/soccer-stats-0.0.2-3.war                           
        def artifactUrl = "http://${NEXUS_URL}/repository/petclinic/com/cg/petclinic/petclinic${BUILD_NUMBER}/petclinic-petclinic${BUILD_NUMBER}.war"

        withEnv(["ARTIFACT_URL=${artifactUrl}", "APP_NAME='petclinic'"]) {
            echo "The URL is ${env.ARTIFACT_URL} and the app name is ${env.APP_NAME}"

            // install galaxy roles
            //sh "ansible-galaxy install -vvv -r provision/requirements.yml -p provision/roles/"    
         
            sh "which ansible"
            sh "whoami"
         
          sh "ansible-galaxy install -vvv -r provision/requirements.yml -p provision/roles/" 
         
            sh "ansible -m ping app_server"
           // sh "ansible-playbook  provision/playbook.yml --extra-vars \" ARTIFACT_URL=${artifactUrl} APP_NAME='soccer-stats' \" "       
         
       // sh "ansible-playbook provision/playbook.yml --extra-vars \"  APP_NAME=soccer-stats ARTIFACT_URL=http://54.70.187.156:8081/repository/demoapp-rele/br/com/meetup/ansible/soccer-stats/0.0.41-/soccer-stats-0.0.2-41.war\" 
     
         
           // ansiblePlaybook colorized: true, 
            //credentialsId: 'playbook',
            //limit: "${HOST_PROVISION}",
            //installation: 'ansible',
            //inventory: 'provision/inventory.ini', 
            //playbook: 'provision/playbook.yml', 
            //sudo: true,
            //sudoUser: 'root' //
                  
       
            //ansiblePlaybook colorized: true, 
            //credentialsId: 'playbook',
            //limit: "${HOST_PROVISION}",
            //installation: 'ansible',
            //inventory: 'provision/inventory.ini', 
            //playbook: 'provision/playbook.yml'
            //sudo: true,
            //sudoUser: 'jenkins'
               
     
                //ansiblePlaybook become: true, colorized: true, 
                //credentialsId: 'ansible', disableHostKeyChecking: true,              
                //extras: 'ARTIFACT_URL="${artifactUrl}" APP_NAME=soccer-demo',
                //inventory: 'provision/inventory.ini',
                //playbook: 'provision/playbook.yml',
                //sudoUser: 'ec2-user' 
                
            
            ansiblePlaybook colorized: true, 
            credentialsId: 'ssh-jenkins',
           // limit: "${HOST_PROVISION}",
            installation: 'ansible',
            inventory: 'provision/inventory.ini', 
            playbook: 'provision/playbook.yml',
            //extra-vars: APP_NAME=soccer-stats ARTIFACT_URL=http://54.70.187.156:8081/repository/demoapp-rele/br/com/meetup/ansible/soccer-stats/0.0.41-/soccer-stats-0.0.2-41.war\" "
            //sudo: true,
             sudoUser: 'jenkins',
             extraVars: [
              ARTIFACT_URL : env.ARTIFACT_URL 
              ]
           
             
           
        }
    } 
}
