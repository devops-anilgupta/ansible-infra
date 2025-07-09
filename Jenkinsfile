pipeline {
    agent any

    parameters {
        choice(name: 'ENV', choices: ['DEV', 'UAT', 'STAGE', 'PROD'], description: 'Choose target environment')
        choice(name: 'PLAYBOOK', choices: [
            'copy-files-internally.yml',
            'jenkins-install.yml',
            'nginx-setup.yml',
            'site.yml'
        ], description: 'Choose which Ansible playbook to run')
    }

    environment {
        INVENTORY_PATH = "inventories/${params.ENV.toUpperCase()}/hosts.ini"
        PLAYBOOK_PATH  = "playbooks/${params.PLAYBOOK}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git(
                    url: 'git@github.com:devops-anilgupta/ansible-infra.git',
                    branch: "${params.ENV}",
                    credentialsId: 'jenkins-master-to-git'
                )
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    echo "Running ${params.PLAYBOOK} against ${params.ENV} environment"
                    sh """
                        ansible-playbook -i "${INVENTORY_PATH}" "${PLAYBOOK_PATH}"
                    """
                }
            }
        }
    }

    post {
        failure {
            echo "Playbook failed!"
        }
        success {
            echo "Playbook ran successfully!"
        }
    }
}
