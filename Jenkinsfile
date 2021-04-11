pipeline {
  agent any
  stages {
    stage('Bash') {
      steps {
        sh '''#!/bin/bash
oc apply -f Yamls/
'''
      }
    }

  }
}