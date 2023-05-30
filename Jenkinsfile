pipeline{
   agent {
        node {
            label 'prod'
        }
    }

   tools {
     maven 'mvn'
   }
   stages {
     stage ("build") {
        steps {
           sh 'mvn clean install'
        }
     }

     stage ("Archive the Artifact"){
        steps{
            archiveArtifacts artifacts: '**/*.jar', followSymlinks: false
        }
    }
    
    stage('jfrog'){
        steps{
            rtUpload (
                serverId: 'jfrog',
                spec: '''{
                      "files": [
                        {
                           "pattern": "*.jar",
                           "target": "test-repo-local"
                        }
                     ]
                }'''
            )
        }
    }
    
    stage('downloading artifact') {
        steps {
            script {
                // Define the JFrog Artifactory URL
                def artifactoryUrl = 'http://192.168.20.115/artifacts'
                sh 'curl -fL https://getcli.jfrog.io | sh'
                sh 'export PATH="$PATH:$HOME/.jfrog/jfrog"'
                sh 'jfrog rt dl example-repo-local/my-app-1.0-SNAPSHOT.jar'
            }
        }
    }
    
    stage('run artifact'){
        steps{
            sh 'java -jar my-app-1.0-SNAPSHOT.jar'
        }
    }
  }
}
