// Define necessary variables for usage in SlackSend
def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
]
def getBuildUser() {
    return currentBuild.rawBuild.getCause(Cause.UserIdCause).getUserId()
}

// Pipeline Definition Start
pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    environment {
        BUILD_USER = ''
    }

    // Parameters that will be visible in the UI for Selection
	parameters {
        gitParameter(branch: '',
                branchFilter: 'origin/(.*)',
                defaultValue: 'master',
                description: '',
                name: 'Select_Branch',
                quickFilterEnabled: false,
                selectedValue: 'NONE',
                sortMode: 'NONE',
                tagFilter: '*',
                type: 'PT_BRANCH')
        extendedChoice(
                name: 'Layers',
                multiSelectDelimiter: ',',
                description: 'Testing',
                type: 'PT_CHECKBOX',
                value:'networking,securitygroups,iam',
                defaultValue: 'networking',
                visibleItemCount: 5
        )
		choice (name: 'Region',
			   choices: ['uk'],
			   description: 'Deployment Regions')
        choice (name: 'Environment',
			   choices: ['test'],
			   description: 'Deployment Environment')
		choice (name: 'Action',
				choices: [ 'plan', 'apply', 'destroy'],
				description: 'Run terraform plan / apply / destroy')
    }

    // Stages Perform Grunt work of Checking out and Terraforming
	stages {
        stage("plan") {
            when { expression { params.Action.contains("plan")}}
            steps{
                dir("jenkins-testing/${params.Layers}") {
                    sh "terraform plan -input=false -var-file=${params.Region}${params.Environment}.tfvars"
                }
            }
        }
        stage("apply") {
            when { expression { params.Action.contains("apply")}}
            steps{
                dir("jenkins-testing/${params.Layers}") {
                    sh "terraform apply -input=false -auto-approve -var-file=${params.Region}${params.Environment}.tfvars"
                }
            }
        }
        stage("destroy") {
            when { expression { params.Action.contains("destroy")}}
            steps{
                dir("jenkins-testing/${params.Layers}") {
                    sh "terraform destroy -input=false -auto-approve -var-file=${params.Region}${params.Environment}.tfvars"
                }
            }
        }
  	}

}
