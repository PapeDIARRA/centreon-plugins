stage('Source') {
  node {
    sh 'setup_centreon_build.sh'
    dir('centreon-plugins') {
      checkout scm
    }
    sh './centreon-build/jobs/plugins/plugins-source.sh'
    source = readProperties file: 'source.properties'
    env.VERSION = "${source.VERSION}"
    env.RELEASE = "${source.RELEASE}"
  }
}

try {
  stage('Package') {
    parallel 'centos7': {
      node {
        sh 'setup_centreon_build.sh'
        sh './centreon-build/jobs/plugins/plugins-package.sh centos7'
      }
    }
    if ((currentBuild.result ?: 'SUCCESS') != 'SUCCESS') {
      error('Package stage failure.');
    }
  }
} catch(e) {
  if (env.BRANCH_NAME == 'master') {
    slackSend channel: "#monitoring-metrology", color: "#F30031", message: "*FAILURE*: `CENTREON PLUGINS` <${env.BUILD_URL}|build #${env.BUILD_NUMBER}> on branch ${env.BRANCH_NAME}\n*COMMIT*: <https://github.com/centreon/centreon-plugins/commit/${source.COMMIT}|here> by ${source.COMMITTER}\n*INFO*: ${e}"
  }
}
