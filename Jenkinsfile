currentBuild.displayName = "Final_Demo # "+currentBuild.number

   def getDockerTag(){
        def tag = sh script: 'git rev-parse HEAD', returnStdout: true
        return tag
        }
        

pipeline{
        agent any  
        environment{
	    Docker_tag = getDockerTag()
        }
        
        stages{


              stage('Quality Gate Statuc Check'){

               agent {
                docker {
                image 'maven'
                args '-v $HOME/.m2:/root/.m2'
                }
            }
                  steps{
                      script{
                      withSonarQubeEnv('second') { 
                      sh "mvn sonar:sonar"
                       }
                      timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
		    sh "mvn clean install"
                  }
                }  
              }



              stage('build')
                {
              steps{
                  script{
		 sh 'cp -r ../devops-training@2/target .'
                   sh 'docker build . -t saikrish12345/java-sample:1'
		   withCredentials([string(credentialsId: 'Student#0509', variable: 'Student#0509')]) {
				    
				  sh 'docker login -u deekshithsn -p Student#0509'
				  sh 'docker push saikrish12345/java-sample:1'
			}
                       }
                    }
                 }
		 
// 		stage('ansible playbook'){
// 			steps{
// 			 	script{
// 				    sh '''final_tag=$(echo $Docker_tag | tr -d ' ')
// 				     echo ${final_tag}test
// 				     sed -i "s/docker_tag/$final_tag/g"  deployment.yaml
// 				     '''
// 				    ansiblePlaybook become: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml'
// 				}
// 			}
// 		}
		
	
		
               }
	       
	       
	       
	      
    
}
