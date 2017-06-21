#!groovy

properties([
  parameters([
    string(
      name: 'K8S_CLUSTER',
      defaultValue: 'kubernetes.demo',
      description: 'The Kubernetes Cluster you want to deploy to',
    ),
  ])
])

node('jenkins-docker-2') {
  ws {
    try {
      def conf = [
        TRAEFIK_VERSION: 'v1.0.0',
        JENKINS_DEPLOY: 'true',
      ]

      stage('Checkout') {
        checkout scm
      }

      stage("Deploy") {
        def Boolean dryrun = conf.JENKINS_DEPLOY != 'true'

        kubernetesDeploy(conf, [k8sCluster: env.K8S_CLUSTER, dryrun: dryrun])
      }
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
