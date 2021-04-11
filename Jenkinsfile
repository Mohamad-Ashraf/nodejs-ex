pipeline {
  agent any
  stages {
    stage('Create') {
      steps {
        sh '''#!/bin/bash
oc apply -f Yamls/
'''
      }
    }

    stage('Trigger Build') {
      steps {
        openshiftBuild 'drupal'
      }
    }

  }
}