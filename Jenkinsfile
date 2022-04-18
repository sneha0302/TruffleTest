pipeline{
   
    agent {label 'linagent'}
	options {
    skipDefaultCheckout()
  }    
    stages{
        stage('build'){
        steps{
            sh 'echo hello'
           // sh 'git clone https://github.com/charankk21/TruffleTest.git'
     
	sh 'docker pull gesellix/trufflehog'
	sh 'docker run gesellix/trufflehog --json --regex https://github.com/charankk21/TruffleTest.git > result'
	sh 'cat result'
	        }
        }
    }
}