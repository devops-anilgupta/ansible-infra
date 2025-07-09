pipeline {
    agent any

    parameters {
        choice(
            name: 'ENV',
            choices: ['DEV', 'UAT', 'STAGE', 'PROD'],
            description: 'Choose target environment'
        )
        choice(
            name: 'PLAYBOOK',
            choices: [
                'copy-files-internally.yml',
                'jenkins-install.yml',
                'nginx-setup.yml',
                'site.yml'
            ],
            description: 'Choose which Ansible playbook to run'
        )
    }

    stages {
        stage('Checkout Code') {
            steps {
                git(
                    url: 'git@github.com:devops-anilgupta/ansible-infra.git',
                    branch: "${params.ENV.toLowerCase()}",
                    credentialsId: 'jenkins-master-to-git'
                )
                echo "Checked out code for ${params.ENV} environment completed!"
                sh 'ls -la'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    def inventoryPath = "inventories/${params.ENV}/hosts.ini"
                    def playbookPath  = "playbooks/${params.PLAYBOOK}"

                    echo "Running ${params.PLAYBOOK} against ${params.ENV} environment"

                    def result = sh(
                        script: """
                            ssh ubuntu@ansible-master '
                                cd /home/ubuntu/ansible_project &&
                                ansible-playbook -i "${inventoryPath}" "${playbookPath}"
                            '
                        """,
                        returnStatus: true
                    )

                    if (result == 0) {
                        echo "✅ Playbook ran successfully"
                    } else {
                        error("❌ Playbook failed with exit code: ${result}")
                    }
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Playbook completed successfully!"
        }
        failure {
            echo "❌ Playbook failed!"
        }
    }
}
