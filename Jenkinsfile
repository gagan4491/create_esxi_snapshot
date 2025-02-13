#### this is s sample file  , you need to set upthe mailserver to use the mail feature.




pipeline {
    agent any
    parameters {
        string(name: 'ip', defaultValue: '192.168.4.1', description: 'The IP address')
        string(name: 'name', defaultValue: 'this field shoudld not be empty  and has atleast 7 character ', description: 'The name parameter, must not be empty')
       // string(name: 'EMAIL_RECIPIENTS', defaultValue: 'gagan.singh@alertdriving.com , jesus.bianco@alertdriving.com', description: 'Comma-separated list of email recipients')
        string(name: 'EMAIL_RECIPIENTS', defaultValue: 'gagan.singh@alertdriving.com ', description: 'Comma-separated list of email recipients')


    }

    stages {


        stage('Preparation') {
            steps {
                cleanWs()
            }
        }
         stage('Setup Temp Directory') {
            steps {
                script {
                    sh'''
                   sudo mkdir -p /opt/exsi_snapshot@tmp
                    sudo chown -R jenkins:jenkins /opt/exsi_snapshot@tmp
                    sudo chmod -R 755 /opt/exsi_snapshot@tmp
                    '''
                }
            }
        }

        stage('Validate Parameters') {
            steps {
                script {
                    // Validation to ensure 'name' is not empty
                    if (params.name.trim() == '') {
                        error "The name parameter cannot be empty. Please provide a valid name."
                    }
                }
            }
        }
        stage('Check IP Count') {
            steps {
                 script {
                     def ipList = params.ip.trim().split(',')
                    def uniqueSortedIps = ipList.collect { it.trim() }.toSet().sort()
                    def ipCount = ipList.size()
                    echo "Unique Sorted IPs: ${uniqueSortedIps.join(', ')}"
                    if (ipCount > 5) {
                    error("Too many IPs provided: ${ipCount}. Maximum allowed is 5.")
                    } else {
                    echo "Valid IP count: ${ipCount}"
                    }

                }
             }
        }
        // stage('Example Usage') {
        //     steps {
        //         echo "Using IP: ${params.ip}"
        //         echo "Using Name: ${params.name}"
        //     }
        // }

        stage('Take Snapshot Stage') {
            steps {
                dir('/opt/exsi_snapshot'){
                    sh """
                    ls -la
                    rm -rf /opt/exsi_snapshot/vm_snapshots_*.xlsx
                    python3 /opt/exsi_snapshot/multiple_ips.py --ip '${params.ip}' --name '${params.name}'
                    ls -la
                    """
                }
            }
        }
        stage('Copy File') {
                steps {
                    sh 'pwd'
                    sh 'cp /opt/exsi_snapshot/vm_snapshots_*.xlsx .'
                }
        }
    }

    post {
        success {
            script {
            def ipList = params.ip.trim().split(',') // Convert to list
            def uniqueSortedIps = ipList.collect { it.trim() }.toSet().sort() // Remove duplicates & sort
            def sortedIpsString = uniqueSortedIps.join(', ') // Convert to string for email

                emailext (
                    subject: "SUCCESS: Pipeline '${env.JOB_NAME}' (${env.BUILD_NUMBER})",
                    body: """
                        <p> Hi Team, </p>
                        <p> Please find the details for the Job for Build no (${env.BUILD_NUMBER}) as below: </p>
                        <p> <strong>Unique Sorted IPs: ${sortedIpsString}</strong> </p>
                         <p><strong>Snapshot Name: '${params.name}' </strong></p>
                         <p><strong>SUCCESS: Pipeline '${env.JOB_NAME}' (${env.BUILD_NUMBER}):</strong></p>
                         <p>Check console output at <a href="${env.BUILD_URL}">${env.JOB_NAME} #${env.BUILD_NUMBER}</a></p>
                         <p>Thanks,</p>
                         <p>DevOps</p>
                    """,
                    to: "${params.EMAIL_RECIPIENTS}",
                    attachmentsPattern: 'vm_snapshots_*.xlsx', // Attach files from the workspace
                    recipientProviders: [[$class: 'DevelopersRecipientProvider']]
                )
            }

        }


        failure {
            emailext (
                subject: "FAILED: Pipeline '${env.JOB_NAME}' (${env.BUILD_NUMBER})",
                body: """
                <p> Hi Team, </p>
                <p> Please find the details for the Job for Build no (${env.BUILD_NUMBER}) as below : </p>
                <p>      </p>
                <p>FAILED: Pipeline '${env.JOB_NAME}' (${env.BUILD_NUMBER}):</p>
                         <p>Check console output at <a href="${env.BUILD_URL}">${env.JOB_NAME} #${env.BUILD_NUMBER}</a></p>
                         <p>Thanks,</p>
                         <p>DevOps</p>
                         """,
                to: "${params.EMAIL_RECIPIENTS}",

                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
    }


}
