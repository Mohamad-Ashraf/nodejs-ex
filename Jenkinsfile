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
                  timeout(5) { 
                    builds.untilEach(1) {
                      return (it.object().status.phase == "Complete")
                    }
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
                  timeout(5) { 
                    openshift.selector("dc", templateName).related('pods').untilEach(1) {
                      return (it.object().status.phase == "Running")
                    }
                }
            }
        }
      }
    }
  }
}
