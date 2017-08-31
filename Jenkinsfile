node {
    try {
    
        // Mark the code build 'stage'
        stage 'Build'

        deleteDir()

        checkout scm

        def version = env.BRANCH_NAME
        def workspace = pwd()
        
        if(env.BRANCH_NAME=="master") {
            version = sh(returnStdout: true, script: 'git describe --abbrev=0 --tags').trim()
            checkout([$class: 'GitSCM', branches: [[name: "tags/${version}"]], extensions: [[$class: 'CleanCheckout']]])
        }
        
        sh("./build.sh develop")

	
	stage 'Running playbook'
	ansiblePlaybook(
		playbook: '/home/abeeda_t/ansible-examples/lamp_simple/site.yml',
		inventory: '/home/abeeda_t/ansible-examples/lamp_simple/hosts')
		
	stage 'Deploy'
       
        emailext attachLog: true, body: "Build succeeded (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} succeeded", to: "${env.EMAIL_RECIPIENTS}"
	
    }
    catch(error) {
        emailext attachLog: true, body: "Build failed (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} failed", to: "${env.EMAIL_RECIPIENTS}"
        currentBuild.result = "FAILED"
        throw error
    }
}
