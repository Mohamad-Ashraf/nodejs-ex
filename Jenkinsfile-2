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
    
    
    stage('build') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  def builds = openshift.selector("bc", drupal).related('builds')
                  timeout(5) { 
                    builds.untilEach(1) {
                      return (it.object().status.phase == "Complete")
                    }
                  }
                }
            }
        }
      }
    }
    stage('deploy') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  def rm = openshift.selector("dc", drupal).rollout()
                  timeout(5) { 
                    openshift.selector("dc", drupal).related('pods').untilEach(1) {
                      return (it.object().status.phase == "Running")
                    }
                  }
                }
            }
        }
      }
    }  

  }
}
