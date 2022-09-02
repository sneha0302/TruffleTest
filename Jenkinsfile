pipeline{
    agent any
    stages{
	stage('Pre-Commit--> Trufflehog'){
            agent {label ''}
	        options {
                skipDefaultCheckout()
                }
			steps{
				sh 'echo hello'
				sh 'docker pull gesellix/trufflehog'
				sh 'docker run gesellix/trufflehog --json --regex  > '
				sh 'cat '
              }
        }
stage('SCA-->Snyk') {
            agent any
            steps{
				checkout([$class: 'GitSCM', branches: [[name: '*/']], extensions: [], userRemoteConfigs: [[url: '']]])
				snykSecurity failOnIssues: false, organisation: '', snykInstallation: '', snykTokenId: ''
                }
        }
	}
}