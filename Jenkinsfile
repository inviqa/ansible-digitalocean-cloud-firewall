def failureMessages = []
def DO_TOKEN_CREDENTIAL_ID = 'digitalocean-ansible-roles-oauth-token'
def SLACK_TOKEN_CREDENTIAL_ID = 'inviqa-slack-integration-token'

pipeline {
    agent {
        docker {
            label 'linux-amd64'
            alwaysPull true
            image 'quay.io/inviqa_images/ansible:2.15-python3.10-trixie'
            args '--entrypoint=""'
        }
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
    }

    environment {
        ANSIBLE_COLLECTIONS_PATH = ".ansible/collections:/home/ansible/.ansible/collections:/usr/share/ansible/collections"
        ANSIBLE_FORCE_COLOR = 'true'
        ANSIBLE_ROLES_PATH = "tests/roles:.ansible/roles:/home/ansible/.ansible/roles"
        SLACK_NOTIFICATION_CHANNEL = 'ops-integrations'
    }

    parameters {
        booleanParam(
            name: 'RUN_LIVE_TESTS',
            defaultValue: true,
            description: 'Run the DigitalOcean live integration tests.'
        )
    }

    stages {
        stage('Install Ansible dependencies') {
            steps {
                sh 'ansible-galaxy collection install -r tests/requirements.yml -p .ansible/collections'
            }
            post {
                failure {
                    script { failureMessages << 'Ansible collection installation failed' }
                }
            }
        }

        stage('Syntax checks') {
            steps {
                sh 'ansible-playbook --syntax-check -i tests/inventory tests/playbook.yml'
                sh 'ansible-playbook --syntax-check -i tests/inventory tests/playbook_cleanup.yml'
            }
            post {
                failure {
                    script { failureMessages << 'Ansible playbook syntax checks failed' }
                }
            }
        }

        stage('Live DigitalOcean tests') {
            when {
                expression { return params.RUN_LIVE_TESTS }
            }
            steps {
                script {
                    withCredentials([
                        string(credentialsId: DO_TOKEN_CREDENTIAL_ID, variable: 'DIGITAL_OCEAN_API_TOKEN')
                    ]) {
                        try {
                            sh "ansible-playbook -i tests/inventory tests/playbook.yml"
                        } finally {
                            sh "ansible-playbook -i tests/inventory tests/playbook_cleanup.yml"
                        }
                    }
                }
            }
            post {
                failure {
                    script { failureMessages << 'Live DigitalOcean integration tests failed' }
                }
            }
        }
    }

    post {
        failure {
            script {
                def message = "ansible-digitalocean-cloud-firewall: ${env.JOB_BASE_NAME} #${env.BUILD_NUMBER} - Failure after ${currentBuild.durationString.minus(' and counting')} (<${env.RUN_DISPLAY_URL}|View Build>)"
                def fallbackMessages = [ message ]
                def fields = []

                def failureMessage = failureMessages.join("\n")
                if (failureMessage) {
                    fields << [
                        title: 'Reason(s)',
                        value: failureMessage,
                        short: false
                    ]
                    fallbackMessages << failureMessage
                }
                def attachments = [
                    [
                        text: message,
                        fallback: fallbackMessages.join("\n"),
                        color: 'danger',
                        fields: fields
                    ]
                ]

                slackSend(channel: env.SLACK_NOTIFICATION_CHANNEL, color: 'danger', attachments: attachments, tokenCredentialId: SLACK_TOKEN_CREDENTIAL_ID)
            }
        }
        always {
            sh 'rm -f tests/test_variables.yml'
            cleanWs()
        }
    }
}
