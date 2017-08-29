#!groovy

properties([
  parameters([
    string(
      name: 'K8S_CLUSTER',
      defaultValue: 'kubernetes.demo',
      description: 'The Kubernetes Cluster you want to deploy to',
    ),
    string(
      name: 'ACEME_EMAIL',
      defaultValue: 'hans.flaatten@evry.com',
      description: 'LetsEncrypt ACEME registration email',
    ),
  ])
])

@Library('utils')

import no.evry.Config

node('jenkins-docker-3') {
  ws {
    try {
      // Jenkins pipelines are "dumb" in that regards that it does not do
      // anything unless you explicitly tells it so. Here we checkout the
      // repository in order to the source code into the workspace.
      stage('Checkout') {
        checkout scm
      }

      // Configure which branches should get the following configurations. The
      // configurations are stored in the /build directory and controles the
      // Jenkins build and Kubernetes deployment.
      def envPatterns = [
        [env: 'prod', regex: /^master$/],
      ]

      config = new Config(this).branchProperties(envPatterns)
      config.ACEME_EMAIL = env.ACEME_EMAIL
      config.K8S_CLUSTER = env.K8S_CLUSTER

      stage("Deploy") {
        def Boolean dryrun = config.JENKINS_DEPLOY != 'true'

        kubernetesDeploy(config, [k8sCluster: config.K8S_CLUSTER, dryrun: dryrun])
      }

    // Catch abort build interrupts and possible handle them differently. They
    // should not be reported as build failures to Slack etc.
    } catch (InterruptedException e) {
      throw e

    // Catch all build failures and report them to Slack etc here.
    } catch (e) {
      throw e

    // Clean up the workspace before exiting. Wait for Jenkins' asynchronous
    // resource disposer to pick up before we close the connection to the worker
    // node.
    } finally {
      step([$class: 'WsCleanup'])
      sleep 10
    }
  }
}
