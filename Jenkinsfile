#!/usr/bin/env groovy

def getBuildNumber() {
  stage 'Get Build Number'
  def job = build job: 'ios-buildnumber'
  env.BUILD_ID = job.getNumber()
}

node('your node name') {
  echo "Starting Pipeline"
  stage 'Clear WorkSpace'
  deleteDir()

  stage 'Checkout'
  dir('app') {
    echo "BranchName: ${env.BRANCH_NAME}"
    git url: 'your git URL',
    branch: "${env.BRANCH_NAME}"
    latest_commit = sh(script:'git log -n 1 --pretty="%s"', returnStdout: true)
    echo "latest_commit: ${latest_commit}"
    if(latest_commit.contains("[ci-skip]")){
      echo "skip"
      stage 'Check Gemfile'
      stage 'Unit Test'
      stage 'Build'
    } else {
      stage 'Check Gemfile'
      sh "bundle install"

      stage 'Unit Test'
      sh "bundle exec fastlane test"

      if (env.BRANCH_NAME.contains("release")) {
        getBuildNumber()
        stage 'Build'
        sh "bundle exec fastlane production"
      } else if (env.BRANCH_NAME.contains("develop")) {
        getBuildNumber()
        stage 'Build'
        sh "bundle exec fastlane beta"
      }
    }
  }
  stage 'Clear WorkSpace After Success'
  deleteDir()
}
