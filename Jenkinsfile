pipeline{
    agent any
    stages{
	stage('Pre-Commit--> Trufflehog'){
            agent {label 'linagent'}
	        options {
                skipDefaultCheckout()
                }
			steps{
				sh 'echo hello'
				sh 'docker pull gesellix/trufflehog'
				sh 'docker run gesellix/trufflehog --json --regex https://github.com/charankk21/TruffleTest.git > result'
				sh 'cat result'
              }
        }
stage('SCA-->Snyk') {
            agent any
            steps{
				checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/charankk21/TruffleTest.git']]])
				snykSecurity failOnIssues: false, organisation: 'ea0871ca-05d5-4005-b34a-59f732ba9bb3', snykInstallation: 'snykKey', snykTokenId: 'snykSecrectS'
                }
        }
stage('snyk-jira') {
		steps {
			script{
				catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') 
				{
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-jira-sync-win.exe --orgID=ea0871ca-05d5-4005-b34a-59f732ba9bb3 --token=85e9708a-ceb6-48f9-a656-05beb16f827e --jiraProjectKey=SNYK --configFile=true --jiraTicketType=Task --projectID=d5e50a37-e09a-4613-82d0-deaad1dffd03'
					bat 'exit 0'
				}
			}
		}
	}
	stage('SAST-->SonarQube')
		{
			agent {label 'linagent'}
			options { skipDefaultCheckout()     }
	                steps{
                                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  {
				sh 'echo hello'
				deleteDir()
				sh 'sudo git clone https://github.com/charankk21/SonarQube.git'
				dir('SonarQube'){
				sh 'mvn sonar:sonar -Dsonar.projectKey=SonarTest -Dsonar.host.url=http://localhost:9000 -Dsonar.login=12a39a18aacff4ee3e3c8d425463919a5d5c7fd2'
				sh 'exit 0'
				}
			}
    		}
	}
	stage('DAST-->HCL Appscan')
		{
    			agent any
			steps
                              {
                			catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  
                                               {
							appscan application: '4829fff0-964c-47c7-9c36-26ce84ac977a',
							credentials: 'hcldast',
							email: true,
							failBuild: false,
							failBuildNonCompliance: false,
							failureConditions: [],
							name: BUILD_NUMBER,
							scanner: dynamic_analyzer(hasOptions: true, loginPassword: "${params.TestFire_Pass}", loginUser: '${params.UserName}',
							optimization: 'Fast', scanType: 'Staging',
							target: 'https://demo.testfire.net?mode=demo'),
							target:'', type: 'Dynamic Analyzer', wait: false
                					bat 'exit 0'
						}
				}
                    }
        stage("Snyk-Container Scan")
	{
    		agent any
    		steps
		{
                    withCredentials([string(credentialsId: 'CK_snykKey', variable: 'snyktoken')]) 
                       {
	                     catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  
                               {
                        		bat 'C:\\Users\\Administrator\\Downloads\\snyk-win.exe  auth ' +snyktoken 
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-win.exe container test alpine:latest'
                        		bat 'exit 0'
		                }
        		}
    		}
	}
stage('Checkov ->Checkout'){
        agent any
        steps{
            script{
            deleteDir()
           bat "git clone https://github.com/charankk21/TruffleTest.git"
           stash "TruffleTest"
            }
        }
        
    }
    stage('Checkov scan') {
        agent{
             docker {
                image 'kennethreitz/pipenv:latest'
                args '-u root --privileged -v /var/run/docker.sock:/var/run/docker.sock'
                label 'linagent'
                         }
               }
        options { skipDefaultCheckout() }      
        steps {
            script {
                  unstash "TruffleTest"
                    sh "ls -al"
                    dir('TruffleTest'){
                    sh "ls -al"
                    sh "pipenv install"
                    sh "pipenv run pip install checkov"
                    sh "pipenv run checkov --directory terraform/ --quiet --download-external-modules true --framework terraform -o json > Checkov.json || true"
                    def report_name='Checkov.json'
                    stash allowEmpty: true, includes: report_name, name: report_name 
                   }
                 }  
              }
          }
	stage('defect-dojo')
		{
			agent any
			steps
			{
				script
					{
		                                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  {
						def report_name='Checkov.json'
			                       //stash allowEmpty: true, includes: report_name, name: 'Checkov.json'
			                       ws('C:\\Users\\Administrator\\defectdojo_api\\examples\\v2')
						{
							unstash report_name
							bat 'python dojo_ci_cd.py --product=2 --file '+report_name+' --scanner="Checkov Scan" --high=0 --host=https://demo.defectdojo.org --api_token=548afd6fab3bea9794a41b31da0e9404f733e222 --user=admin --engagement=1 --active TRUE'
			                         }
						bat 'exit 0'
					}
				}
			}
		}
	}
}
