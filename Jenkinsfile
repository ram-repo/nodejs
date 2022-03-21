pipline {
  agent{label nodejs}
  stage('Sonarqube EnterPrise Scan') {
        /* groovylint-disable-next-line SpaceAfterClosingBrace */
        steps {
          withSonarQubeEnv('Sonar Ent Tooling') {
            container('lambda-app-builder') {
                script {
                  if (fileExists("build.gradle")){
                  def projectName = env.JOB_NAME.split("/")[1]
                  def orgName = projectName.split("-")[0,1].join()
                  sh """cat << EOF > gradle.properties
sonar.projectKey=com.mcd.${orgName}:${projectName}
sonar.projectName=${projectName}
EOF"""
                  sh "chmod +x gradlew"
                  sh  "./gradlew sonarqube -Dsonar.projectKey=com.mcd.${orgName}:${projectName} -Dsonar.projectName=${projectName}"
                  
                } else if (fileExists("pom.xml")) {
                  sh 'mvn -Dhttp.keepAlive=false -Dmaven.wagon.http.pool=false -Dmaven.wagon.http.retryHandler.count=10 clean install sonar:sonar -X'
                } else if (fileExists("package.json")){
                  def projectName = env.JOB_NAME.split("/")[1]
                  def orgName = projectName.split("-")[0,1].join()
                  def test-report-path = test-report.xml
                  sh """cat << EOF > sonar-project.properties
sonar.projectKey=com.mcd.${orgName}:${projectName}
sonar.projectName=${projectName}
sonar.testExecutionReportPaths=${test-report-path}
sonar.javascript.lcov.reportPaths=test-coverage/lcov.info
EOF"""
                  sh "npm install"
                  sh "npm run coverage-lcov"
                  sh "sonar-scanner \
                  -Dsonar.projectKey=com.mcd.${orgName}:${projectName} \
                  -Dsonar.projectName=${projectName} -Dsonar.testExecutionReportPaths=${test-report-path} \
                  -Dsonar.javascript.lcov.reportPaths=test-coverage/lcov.info"
                }else {
                  def scannerHome = tool 'Sonar Scanner';
                  def projectName = env.JOB_NAME.split("/")[1]
                  def orgName = projectName.split("-")[0,1].join()
                  sh """cat << EOF > sonar-project.properties
sonar.projectKey=com.mcd.${orgName}:${projectName}
sonar.projectName=${projectName}
EOF"""
                sh "${scannerHome}/bin/sonar-scanner"
              }
            }
          }
        }
      }
    }
}
