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
				snykSecurity failOnIssues: false, organisation: '0fc86b5d-38a2-4a8f-9f5e-90b2c2dec1b1', snykInstallation: 'snykKey', snykTokenId: 'CK_SNYK_TOKEN'
                }
        }
stage('defect-dojo')
{
	agent any
	steps
	{
		script
		{
			def report_name='snyk_report.json'
			stash allowEmpty: true, includes: report_name, name: 'snyk_report.json'
			ws('C:\\Users\\Administrator\\defectdojo_api\\examples\\v2')
			{
				unstash report_name
				bat 'python dojo_ci_cd.py --product=2 --file '+report_name+' --scanner="Snyk Scan" --high=0 --host=https://demo.defectdojo.org --api_token=548afd6fab3bea9794a41b31da0e9404f733e222 --user=admin --engagement=1 --active TRUE'
			}
		}
	}
}
stage ('DAST-->ZAP')
{
	agent any
    steps{
         bat 'java -jar C:\\Users\\Administrator\\Downloads\\OWASP\\ZedAttackProxy\\zap-2.10.0.jar -quickurl http://demo.testfire.net -quickout C:\\result_1.html -quickprogress -cmd'
    }
}
	}
}