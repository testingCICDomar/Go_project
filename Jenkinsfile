pipeline {
    agent any
    stages { 

        stage("Init") {
            steps {
                script {
                    
                    // Show the select input
                    env.CTB = input message: 'User input required', ok: 'Choose!',
                            parameters: [choice(name: 'CTB', choices: ['Continue', 'Abort'], description: 'pipeline is Starting !')]
                
                    
                }

                echo "Choice selected: ${env.CTB}"
                
            }
        }

        stage(test_on_App_Instance){
            when {
                expression { env.CTB == 'Continue'}
            }
            steps {
                script {
                    sh '''
                        ssh -tt ec2-user@3.133.92.205 'Last_Branch=$(tail -n 1 forJenkins/branches.txt );cd application;git checkout ${last_branch};echo '${last_branch}';exit'
                        touch result.txt
                        var=$(ssh ec2-user@3.133.92.205 'bash forJenkins/dirtest.sh')
                        echo ${var} > /tmp/testResult.txt
                        chmod 777 /tmp/testResult.txt
                    '''
                   env.var = readFile (file: "/tmp/testResult.txt")
                   
                }
                echo "==========================================================="
                echo "============================${env.var} ===================="
                echo "==========================================================="
                
            }
        }
            
        stage(test){
            when {
                expression { env.var == 'False'}
            }
            steps {
                script {
                    echo "${env.var} ===================="
                     error "sorry next time !"
                }
            }
        }

        stage(Sonar){
            when {
                expression { env.CTB == 'Continue'}
            }
            steps {
                script {
                    sh 'echo "sonar starting" '
                }
            }
        }

    }
}