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
    stage('create') {
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
    stage('rollout') {
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
