pipeline{
    agent any
    stages{
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
       
	}
}
