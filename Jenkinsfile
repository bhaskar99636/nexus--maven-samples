def gv

pipeline{
    agent any
    environment {
        registry = "webapp"
        registryCredential = 'ACR'
        dockerImage = ''
        registryUrl = 'defsloc.azurecr.io'
    }
    
    stages{
          stage('code quality check via sonarQube') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'dev' || 
                        env.BRANCH_NAME == 'QA' ) {
                        echo 'I execute on the DEV and QA branch'
                        gv = load "pipeline_config.groovy"
                        echo "sonarQube code quality check"
                        gv.qualityanalysis()
                    } else {
                        echo 'I execute elsewhere' 
                  }
                }
            }
        }
           stage('Generate Junit Test Report') {
            steps {
              script {
                  if (env.BRANCH_NAME == 'dev' || 
                      env.BRANCH_NAME == 'QA' ) {
                      gv = load "pipeline_config.groovy"
                      echo "Generated Test report..."
                      gv.testReport()
                } else {
                        echo 'I execute elsewhere' 
                  }
              }
            }
         }
        stage('generate HTML Report') {
          steps {
            script {
                publishHTML (target: [
          allowMissing: false,
          alwaysLinkToLastBuild: false,
          keepAll: true,
          reportDir: 'target',
          reportFiles: 'TEST-com.example.demo.DemoApplicationTests.xml',
          reportName: "RCov Report"
    ])

    }
 }
}
           stage("Build jar") {
            steps {
                script {
                    echo "building jar"
                    gv.buildJar()
                }
            }
         }
        stage('Upload Artifact to Nexus') {
            steps {
                script { 
                    echo "uploading artifact to nexus"
                    gv.uploadArtifactToNexus()
                }
            }
        }
       stage("Roll Back") {
             steps {
                 script {
                   echo "roll back to previous version"
                   gv.rollback()
             }
          }
        }
      stage('Build Docker image') {
         steps{
           script {
             echo "building the docker image"
             gv.buildImage()
     }
  }
}
      stage('Push Image to ACR') {
            steps {
                script {
                    echo "pushing the docker image to ACR"
                    gv.pushImage()
                }
            }
    
        
  }
 }
