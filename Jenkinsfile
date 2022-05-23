pipeline{
    agent any
    stages{
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
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-jira-sync-win.exe --orgID=ea0871ca-05d5-4005-b34a-59f732ba9bb3 --token=snykSecrectS --jiraProjectKey=SNYK --configFile=true --jiraTicketType=Task --projectID=$projectID$'
					bat 'exit 0'
				}
			}
		}
	}
	}
}