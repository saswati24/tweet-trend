
def registry = 'https://saswati05.jfrog.io/'
pipeline{
    agent {
        node{
            label "maven"
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.8/bin:$PATH"
}
    stages{
        stage("Build"){
            steps{
                echo "--------------------build started-----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "--------------------build ended--------------"
            }
        }
        stage("test"){
            steps{
                echo "-----------------unit test started-----------------------"
                sh "mvn surefire-report:report"
                echo "-----------------unit test completed----------------------"
            }
        }
             
         stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog_Creds"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "saswati-libs-release-local",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }   
        
    }
}
