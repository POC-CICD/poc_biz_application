def getNameSpace(nsConfigPath){

    def NS=sh (returnStatus: true, script: "grep -i name: " + nsConfigPath + " |head -n1| cut -d':' -f2 &> namespace.txt")
    output = readFile('namespace.txt').trim()

    output
}

def applyK8Config(configPath){
    sh (returnStatus: true, script: "kubectl apply -f ${configPath}")
}




pipeline {

    agent { label "k8-master" }

    environment {

            appName = 'biz-application'

            /* Do not change below this line! */
            k8configPath = "${WORKSPACE}/install/deploy/kubernetes"
            namespace = getNameSpace("${k8configPath}/namespace-canary.yaml")

            switchTo  = ''
    }

    parameters {
        choice(choices: "10\n20\n30\n40\n50", description: 'Percentage traffic you want to route to UAT deployment', name: 'percentTrafficOnUAT')
    }

    stages {
		stage('Clone Repo') {
			steps {
				checkout scm
			}
			/*steps {
			    	checkout([$class: 'GitSCM',
					branches: [[name: '/master']],
					doGenerateSubmoduleConfigurations: false,
					extensions: [[$class: 'CleanCheckout']],
					submoduleCfg: [],
					//userRemoteConfigs: [[credentialsId: 'b2ed5912-90f0-40fb-8f98-a35933a55f30', url: 'https://github.com/nisum-inc/POC_Discovery_Server.git']]
					userRemoteConfigs: [[credentialsId: 'b2fd807f-ce8d-4c98-aa74-792f70925e88', url: 'https://github.com/nisum-inc/POC_Discovery_Server.git']]

				])
			}*/
		}


		stage('Apply VirtualService') {

		    steps {

                sh "sed -i \'s/weight: 10/weight: ${params.percentTrafficOnUAT}/g\' ${env.k8configPath}/${env.appName}-servicemesh-${env.namespace}.yaml"
                sh "sed -i \'s/weight: 90/weight: 100.sub(${params.percentTrafficOnUAT})/g\' ${env.k8configPath}/${env.appName}-servicemesh-${env.namespace}.yaml"

                script {
                  applyK8Config(env.k8configPath + "/" + env.appName + "-servicemesh-" + env.namespace + ".yaml")
                }


        	}
        }

    }
}
