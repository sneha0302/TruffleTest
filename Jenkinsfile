pipeline{
    agent any
    stages{
	       stage("Snyk-IAC Scan")
              {
    			agent any
			steps
                              {
        				withCredentials([string(credentialsId: 'CK_snykKey', variable: 'snyktoken')]) {
					catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE')  {
					checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/charankk21/TruffleTest.git']]])
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-win.exe auth ' +snyktoken 
			                bat 'C:\\Users\\Administrator\\Downloads\\snyk-win.exe iac test'
            				sh 'exit 0'
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
			def report_name='snyk_report.json'
			stash allowEmpty: true, includes: report_name, name: 'snyk_report.json'
			ws('C:\\Users\\Administrator\\defectdojo_api\\examples\\v2')
			{
				unstash report_name
				bat 'python dojo_ci_cd.py --product=2 --file '+report_name+' --scanner="Snyk Scan" --high=0 --host=https://demo.defectdojo.org --api_token=548afd6fab3bea9794a41b31da0e9404f733e222 --user=admin --engagement=1 --active TRUE'
			}
			bat 'exit 0'
			}
		}
	}
}
	}
}