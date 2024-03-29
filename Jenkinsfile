def GIT_CREDENTIALS='github-username-and-token-as-password'
def CHANNEL = '#eng-test-regression' // set which channel to send regression messages to
def BUILD_TIMESTAMP = ""
def VERSION

pipeline {
  options {
    buildDiscarder(logRotator(daysToKeepStr: '365', numToKeepStr: '50'))
  }
  agent {
    kubernetes {
      label 'docker-base-images'
      idleMinutes 5
      yamlFile 'jenkins/kubernetes/build-pod.yaml'
      inheritFrom 'base'
      defaultContainer 'docker'
   }
  }

  environment {
    BRANCH_ORIGINAL = "${buildContext.branchName(env)}"
    BRANCH_LOWER = "${buildContext.branchName(env).toLowerCase()}"
    IMAGE_TAG = "${BRANCH_LOWER}_${buildContext.gitCommitShort(env)}"
    TERRAFORM_VERSION = "0.14.10"
  }

  parameters {
    booleanParam(name: 'ANDROID_APP', defaultValue: false, description: "Check if you want to build Android App Docker Image")
    booleanParam(name: 'BATCH', defaultValue: false, description: "Check if you want to build Batch Docker Image")
    booleanParam(name: 'CADDY', defaultValue: false, description: "Check if you want to build CADDY Docker Image")
    booleanParam(name: 'DATADOG_HOST_AGENT', defaultValue: false, description: "Check if you want to build Datadog Host Agent Docker Image")
    booleanParam(name: 'DATADOG_JAVA_AGENT', defaultValue: false, description: "Check if you want to build Datadog Java Agent Docker Image")
    booleanParam(name: 'DEPLOYS', defaultValue: false, description: "Check if you want to build DEPLOYS Docker Image")
    booleanParam(name: 'FLYWAY', defaultValue: false, description: "Check if you want to build FLYWAY Docker Image")
    booleanParam(name: 'GRADLE_5_4_1', defaultValue: false, description: "Check if you want to build GRADLE Docker Image")
    booleanParam(name: 'GRADLE_6_6_1', defaultValue: false, description: "Check if you want to build GRADLE Docker Image")
    booleanParam(name: 'JAVA8', defaultValue: false, description: "Check if you want to build JAVA8 Docker Image")
    booleanParam(name: 'LIGHTHOUSE', defaultValue: false, description: "Check if you want to build Lighthouse Docker Image")
    booleanParam(name: 'LOGSTASH', defaultValue: false, description: "Check if you want to build Logstash Docker Image")
    booleanParam(name: 'NGINX_PHP_FPM', defaultValue: false, description: "Check if you want to build NGINX_PHP_FPM Docker Image")
    booleanParam(name: 'NODE_CHROME', defaultValue: false, description: "Check if you want to build NODE_CHROME Docker Image")
    booleanParam(name: 'OPSGENIE', defaultValue: false, description: "Check if you want to build opsgenie Docker Image")
    booleanParam(name: 'PYTHON_DATA_SCHEDULER', defaultValue: false, description: "Check if you want to build python-data-scheduler Docker Image")
    booleanParam(name: 'SKYBOX_NGINX', defaultValue: false, description: "Check if you want to build Skybox Nginx Docker Image")
    booleanParam(name: 'STAGING_PROXY_NGINX', defaultValue: false, description: "Check if you want to build Staging Proxy Nginx Docker Image")
    booleanParam(name: 'SYSLOG', defaultValue: false, description: "Check if you want to build Syslog Docker Image")
    booleanParam(name: 'TERRAFORM', defaultValue: false, description: "Check if you want to build Terraform-Make Docker Image")
    booleanParam(name: 'TOMCAT', defaultValue: false, description: "Check if you want to build Tomcat Docker Image")
    booleanParam(name: 'WIREMOCK_STANDALONE', defaultValue: false, description: "Check if you want to build WIREMOCK_STANDALONE Docker Image")
  }

  stages {
    stage('Build Images') {
      parallel {
        stage('Build Docker - Android App Build') {
          when {
            anyOf {
              changeset "android-app-build/*"
              expression { return params.ANDROID_APP }
            }
          }
          steps {
            dir('android-app-build') {
              sh "docker build --tag=vividseats/android-app-build:${IMAGE_TAG} ."
              sh "docker push vividseats/android-app-build:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Batch') {
          when {
            anyOf {
              changeset "batch/*"
              expression { return params.BATCH }
            }
          }
          steps {
            dir('batch') {
              sh "docker pull vividseats/batch:${IMAGE_TAG} || true"
              sh "docker build --cache-from vividseats/batch:${IMAGE_TAG} --tag=vividseats/batch:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/batch:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Caddy') {
          when {
            anyOf {
              changeset 'caddy/*'
              expression { return params.CADDY }
            }
          }
          steps {
            dir('caddy') {
              sh "docker pull vividseats/caddy:${IMAGE_TAG} || true"
              sh "docker build --cache-from vividseats/caddy:${IMAGE_TAG} --tag=vividseats/caddy:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/caddy:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Datadog Host Agent') {
          when {
            anyOf {
              changeset "datadog-host-agent/*"
              expression { return params.DATADOG_HOST_AGENT }
            }
          }
          steps {
            dir('datadog-host-agent') {
              sh "docker pull vividseats/datadog-host-agent:7-${IMAGE_TAG} || true"
              sh "docker build --cache-from vividseats/datadog-host-agent:7-${IMAGE_TAG} --tag=vividseats/datadog-host-agent:7-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/datadog-host-agent:7-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Datadog Java Agent') {
          environment {
            VERSION = "0.32.0"
          }
          when {
            anyOf {
              changeset "datadog-java-agent/*"
              expression { return params.DATADOG_JAVA_AGENT }
            }
          }
          steps {
            dir('datadog-java-agent') {
              sh "docker pull vividseats/datadog-java-agent:${env.VERSION}-${IMAGE_TAG} || true"
              sh "docker build --build-arg VERSION=${env.VERSION} --cache-from vividseats/datadog-java-agent:${env.VERSION}-${IMAGE_TAG} --tag=vividseats/datadog-java-agent:${env.VERSION}-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/datadog-java-agent:${env.VERSION}-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Flyway') {
          environment {
            VERSION = "8.2.3"
          }
          when {
            anyOf {
              changeset "flyway/*"
              expression { return params.FLYWAY }
            }
          }
          steps {
            dir('flyway') {
              sh "docker build --build-arg FLYWAY_VERSION=${env.version} --tag=vividseats/flyway:${env.VERSION}-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/flyway:${env.VERSION}-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Gradle 5.4.1') {
          when {
            anyOf {
              changeset 'gradle/5.4.1/*'
              expression { return params.GRADLE_5_4_1 }
            }
          }
          steps {
            dir('gradle/5.4.1') {
              sh "docker build --tag vividseats/gradle:5.4.1-jdk11-docker-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/gradle:5.4.1-jdk11-docker-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Gradle 6.6.1') {
          when {
            anyOf {
              changeset 'gradle/6.6.1/*'
              expression { return params.GRADLE_6_6_1 }
            }
          }
          steps {
            dir('gradle/6.6.1') {
              sh "docker build --tag vividseats/gradle:6.6.1-jdk11-docker-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/gradle:6.6.1-jdk11-docker-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Deploys') {
          when {
            anyOf {
              changeset 'deploys/*'
              expression { return params.DEPLOYS }
            }
          }
          steps {
            dir('deploys') {
              sh "docker pull vividseats/deploys:${IMAGE_TAG} || true"
              sh "docker build --cache-from vividseats/deploys:${IMAGE_TAG} --tag=vividseats/deploys:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/deploys:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - java8') {
          when {
            anyOf {
              changeset "java8/*"
              expression { return params.JAVA8 }
            }
          }
          steps {
            dir('java8') {
              sh "docker pull vividseats/java:oracle-8-${IMAGE_TAG} || true"
              sh "docker build --cache-from vividseats/java:oracle-8-${IMAGE_TAG} --tag=vividseats/java:oracle-8-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/java:oracle-8-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Lighthouse') {
          when {
            anyOf {
              changeset "lighthouse/*"
              expression { return params.LIGHTHOUSE }
            }
          }
          steps {
            dir('lighthouse') {
              sh "docker build --tag=vividseats/lighthouse:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/lighthouse:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Opsgenie') {
          when {
            anyOf {
              changeset "opsgenie/*"
              expression { return params.OPSGENIE }
            }
          }
          steps {
            dir('opsgenie') {
              sh "docker build --tag=vividseats/opsgenie-alert:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/opsgenie-alert:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Python Data Scheduler') {
          when {
            anyOf {
              changeset "python-data-scheduler/*"
              expression { return params.PYTHON_DATA_SCHEDULER }
            }
          }
          steps {
            dir('python-data-scheduler') {
              sh "docker build --tag=vividseats/python-data-scheduler:latest ."
              sh "docker push vividseats/python-data-scheduler:latest"
            }
          }
        }
        stage('Build Docker - Skybox Nginx') {
          when {
            anyOf {
              changeset "skybox-nginx/*"
              expression { return params.SKYBOX_NGINX }
            }
          }
          steps {
            dir('skybox-nginx') {
              sh "docker build --tag=vividseats/skybox-nginx:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/skybox-nginx:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Staging Proxy Nginx') {
          when {
            anyOf {
              changeset "staging-proxy-nginx/*"
              expression { return params.STAGING_PROXY_NGINX }
            }
          }
          steps {
            dir('staging-proxy-nginx') {
              dockerBuildAndPush image: 'vividseats/staging-proxy-nginx', tag: "${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - syslog-ng') {
          when {
            anyOf {
              changeset "syslog-ng/*"
              expression { return params.SYSLOG }
            }
          }
          steps {
            dir('syslog-ng') {
              sh "docker pull vividseats/syslog-ng:${IMAGE_TAG} || true"
              sh "docker build --cache-from vividseats/syslog-ng:${IMAGE_TAG}  --tag=vividseats/syslog-ng:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/syslog-ng:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Terraform') {
          when {
            anyOf {
              changeset "terraform-make/*"
              expression { return params.TERRAFORM }
            }
          }
          steps {
            dir('terraform-make') {
              sh "docker pull vividseats/terraform-make:${TERRAFORM_VERSION}-${IMAGE_TAG} || true "
              sh "docker build --cache-from vividseats/terraform-make:${TERRAFORM_VERSION}-${IMAGE_TAG} --tag=vividseats/terraform-make:${TERRAFORM_VERSION}-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/terraform-make:${TERRAFORM_VERSION}-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - Tomcat') {
          when {
            anyOf {
              changeset "tomcat/*"
              expression { return params.TOMCAT }
            }
          }
          steps {
            dir('tomcat') {
              sh "docker pull vividseats/tomcat:7.0.79-${IMAGE_TAG} || true "
              sh "docker build --cache-from vividseats/tomcat:7.0.79-${IMAGE_TAG} --tag=vividseats/tomcat:7.0.79-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/tomcat:7.0.79-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - wiremock-standalone') {
          environment {
            VERSION = "2.20.0"
          }
          when {
            anyOf {
              changeset "wiremock-standalone/*"
              expression { return params.WIREMOCK_STANDALONE }
            }
          }
          steps {
            dir('wiremock-standalone') {
              sh "docker pull vividseats/wiremock-standalone:${env.VERSION}-${IMAGE_TAG} || true"
              sh "docker build --build-arg VERSION=${env.VERSION} --cache-from vividseats/wiremock-standalone:${env.VERSION}-${IMAGE_TAG} --tag=vividseats/wiremock-standalone:${env.VERSION}-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/wiremock-standalone:2.20.0-${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - nginx-php-fpm') {
          when {
            anyOf {
              changeset "nginx-php-fpm/*"
              expression { return params.NGINX_PHP_FPM }
            }
          }
          steps {
            dir('nginx-php-fpm') {
              sh "docker pull vividseats/nginx-php-fpm:${IMAGE_TAG} || true "
              sh "docker build --cache-from vividseats/nginx-php-fpm:${IMAGE_TAG} --tag=vividseats/nginx-php-fpm:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/nginx-php-fpm:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - node-chrome') {
          when {
            anyOf {
              changeset "node-chrome/*"
              expression { return params.NODE_CHROME }
            }
          }
          steps {
            dir('node-chrome') {
              sh "docker build --tag=vividseats/node-chrome:${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/node-chrome:${IMAGE_TAG}"
            }
          }
        }
        stage('Build Docker - logstash') {
          when {
            anyOf {
              changeset "logstash/*"
              expression { return params.LOGSTASH }
            }
          }
          steps {
            dir('logstash') {
              sh "docker build -f Dockerfile_5.5.0 --cache-from vividseats/logstash:5.5.0-${IMAGE_TAG} --tag=vividseats/logstash:5.5.0-${IMAGE_TAG} ."
              sh "docker build -f Dockerfile_6.1.1 --cache-from vividseats/logstash:6.1.1-${IMAGE_TAG} --tag=vividseats/logstash:6.1.1-${IMAGE_TAG} ."
              sh "docker build -f Dockerfile_7.6 --cache-from vividseats/logstash:7.6-${IMAGE_TAG} --tag=vividseats/logstash:7.6-${IMAGE_TAG} ."
            }
          }
          post {
            success {
              sh "docker push vividseats/logstash:5.5.0-${IMAGE_TAG}"
              sh "docker push vividseats/logstash:6.1.1-${IMAGE_TAG}"
              sh "docker push vividseats/logstash:7.6-${IMAGE_TAG}"
            }
          }
        }
      }
    }
  }
}
