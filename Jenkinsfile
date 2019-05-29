pipeline {
  agent any
  options {
    ansiColor('xterm')
    timestamps()
  }
  stages {
    stage('build') {
      environment {
        BUILD_CACHE_USER = vault path: '/kv/jenkins/gradle-build-cache', key: 'username'
        BUILD_CACHE_PASSWORD = vault path: '/kv/jenkins/gradle-build-cache', key: 'password'
        ORG_GRADLE_PROJECT_nexusUsername = vault path: '/kv/jenkins/nexus', key: 'username'
        ORG_GRADLE_PROJECT_nexusPassword = vault path: '/kv/jenkins/nexus', key: 'password'
      }
      steps {
        configFileProvider([configFile(fileId: 'gradle-init', targetLocation: 'init.gradle')]) {
          sh './gradlew --init-script init.gradle --continue --build-cache --parallel uploadArchives'
          // if tests were not re-run touch the files so jenkins doesn't complain about the timestamps
          sh 'find .  -type f  -name \'*.xml\' -exec touch {} +'
          junit allowEmptyResults: true, testResults: '**/build/test-results/test/*.xml'
        }
      }
    }
  }
}
