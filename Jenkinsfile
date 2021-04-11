// def templatePath = 'https://raw.githack.com/MuhammadYasser2019/nodejs-ex/master/template-nodejs.json' 
def templatePath = 'https://raw.githack.com/MuhammadYasser2019/nodejs-ex/master/drupal.yaml'
// def templateName = 'nodejs-mongodb-example'
def templateName = 'drupal'

pipeline {
  agent {
    node {
      label 'nodejs' 
    }
  }
  options {
    timeout(time: 20, unit: 'MINUTES') 
  }
  stages {
    stage('preamble') {
        steps {
            script {
                openshift.withCluster() {
                    openshift.withProject() {
                        echo "Using project: ${openshift.project()}"
                    }
                }
            }
        }
    }
    stage('CheckOut') {
      steps {
        sh '''#!/bin/bash
            oc apply -f Yamls/
            '''
      }
    }
    // stage('create') {
    //   steps {
    //     script {
    //         openshift.withCluster() {
    //             openshift.withProject() {
    //               openshift.newApp(templatePath) 
    //             }
    //         }
    //     }
    //   }
    // }
    stage('build') {
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
    stage('buildTime') {
      steps {
        script {
            openshift.withCluster() {
                openshift.withProject() {
                  def builds = openshift.selector("bc", drupal).related('builds')
                  timeout(7) { 
                    builds.untilEach(1) {
                      return (it.object().status.phase == "Complete")
                    }
                  }
                }
            }
        }
      }
    }
    stage('deployTime') {
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