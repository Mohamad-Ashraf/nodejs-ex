pipeline {
  agent any
  stages {
    stage('CheckOut') {
      steps {
        sh '''#!/bin/bash
oc apply -f Yamls/
'''
      }
    }

    stage('Create') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject() {
              openshift.selector("bc", "drupal").startBuild()
            }
          }
        }

      }
    }

    stage('RollOut') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject() {
              openshift.selector("dc", "drupal").rollout().latest()
            }
          }
        }

      }
    }

  }
}