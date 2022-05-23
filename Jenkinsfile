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
					bat 'C:\\Users\\Administrator\\Downloads\\snyk-jira-sync-win.exe --orgID=ea0871ca-05d5-4005-b34a-59f732ba9bb3 --token=85e9708a-ceb6-48f9-a656-05beb16f827e --jiraProjectKey=SNYK --configFile=true --jiraTicketType=Task --projectID=d5e50a37-e09a-4613-82d0-deaad1dffd03'
					bat 'exit 0'
				}
			}
		}
	}
	}
}
