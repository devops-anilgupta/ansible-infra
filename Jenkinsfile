pipeline {
    agent any

    parameters {
        choice(name: 'ENV', choices: ['dev', 'uat', 'stage', 'prod'], description: 'Choose target environment')
        choice(name: 'PLAYBOOK', choices: [
            'copy-files-internally.yml',
            'jenkins-install.yml',
            'nginx-setup.yml',
            'site.yml'
        ], description: 'Choose which Ansible playbook to run')
    }

    environment {
        INVENTORY_PATH = "inventories/${params.ENV}/hosts.ini"
        PLAYBOOK_PATH  = "playbooks/${params.PLAYBOOK}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'git@github.com:your-org/ansible_project.git', branch: 'main'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    echo "Running ${params.PLAYBOOK} against ${params.ENV} environment"
                    sh """
                    ansible-playbook -i ${INVENTORY_PATH} ${PLAYBOOK_PATH}
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
