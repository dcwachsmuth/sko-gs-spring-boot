pipeline {
  options {
    buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '0'))
  }
  agent {
    kubernetes {
      //cloud 'kubernetes'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: danielrmartin/sko:1.2
    command: ['cat']
    tty: true
"""
    }
  }
  parameters {
    choice(
      choices: ['deploy' , 'release'],
      description: '',
      name: 'FLOW_CONTEXT'
    )
  }
  stages {
    stage('Run Maven') {
      steps {
        container('maven') {
          sh 'mvn deploy -f ./complete/pom.xml'
        }
      }
    }
    stage ('Run Sonarqube'){
      steps{
        container('maven'){
          sh 'mvn sonar:sonar -f ./complete/pom.xml'
        }}}
  }
  post {
    success {
      script {
        if (params.FLOW_CONTEXT == 'deploy') {
          step([$class: 'ElectricFlowPipelinePublisher',
            configuration: 'user16',
            projectName: 'TestDW',
            pipelineName: 'testpipelinedw',
            addParam: '{"pipeline":{"pipelineName":"testpipelinedw","parameters":"[{\\\"parameterName\\\": \\\"jenkinsJobName\\\", \\\"parameterValue\\\": \\\"'+"${env.JOB_NAME}"+'\\\"},{\\\"parameterName\\\": \\\"jenkinsBuildNumber\\\", \\\"parameterValue\\\": \\\"'+"${env.BUILD_NUMBER}"+'\\\"}]"}}'
          ])
        }
      }
    }
   }
}
