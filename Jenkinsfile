pipeline {
  agent any
  parameters {
    choise(name: "playbook", choises: ["preparation.yaml", "set-k8s-sysctl.yaml", "setup-app.yaml"])
    string(name: "host", trim: true)
    gitParameter (name: 'branch', type: 'PT_BRANCH', quickFilterEnabled: true, defaultValue: 'main')
    booleanParam(name: "check", defaultValue: true)
  }

  stages {
    stage('Checkout repo'){
      steps {
        checkout([$class: 'GitSCM', branches: [[name: "${branch}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins-key', url: "$git_url"]]])
      }
    }
    stage('Check') {
      when {
        expression { params.check }
      }
      steps {
        sh """
          export ANSIBLE_HOST_KEY_CHECKING=False
          ansible-playbook -i '$params.host,' '$params.playbook' --check
        """
      }
    }
    stage('Apply') {
      when {
         expression { params.check == "false" }
      }
      steps {
        sh """
          export ANSIBLE_HOST_KEY_CHECKING=False
          ansible-playbook -i '$params.host,' '$params.playbook' --diff
        """
      }
    }
  }
}
