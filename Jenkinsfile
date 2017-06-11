#!groovy

node('jenkins-docker-2') {
  ws {
    try {
      stage('Checkout') {
        checkout scm
      }

      stage('Test') {
        docker.image('node:8-alpine').inside {
          sh 'node --version'
        }
      }

      /*stage("Deploy") {
        kubernetesDeploy([:], [
          path: 'deploy',
          k8sCluster: 'kubernetes.starefossen.azure'
        ])
      }*/
    } catch (InterruptedException e) {
      throw e
    } catch (e) {
      throw e
    } finally {
      step([$class: 'WsCleanup'])
      // Wait for Jenkins asynchronous resource disposer to pick up before we
      // close the connection to the worker node.
      sleep 10
    }
  }
}
