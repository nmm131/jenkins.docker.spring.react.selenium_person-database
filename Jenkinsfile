pipeline {
    agent none
    stages {
		stage('Run') {
			parallel {
				stage('Back-end') {
					agent {
						docker {
							image 'jamesdbloom/docker-java8-maven:latest'
							args '-v /root/.m2:/root/.m2 -p 8050:8050'
						}
					}
					stages {
						stage('Set Up') {
							steps {
								script {
									sh 'rm -rf back-end'
								}
							}
						}
						stage('SCM Checkout') {
							steps {
								script {
									sh 'git clone https://github.com/nmm131/jenkins.docker.spring.react.selenium_person-database.git back-end'
								}
							}
						}
						stage('Compile-Package-Test') {
							steps {
								script {
									dir('./back-end/webservice') {
										sh "mvn spring-boot:run &"
										sh "sleep 8m"
										sh "echo 'Closing Spring Application'"
									}
								}
							}
						}
					}
				}
				stage('Front-end') {
					agent {
						docker {
							image 'node:10'
							args '-v /root/.m2:/root/.m2 -p 8060:8060'
						}
					}
					stages {
						stage('Set Up') {
							steps {
								script {
									sh 'rm -rf front-end'
								}
							}
						}
						stage('SCM Checkout') {
							steps {
								script {
									sh 'git clone https://github.com/nmm131/jenkins.docker.spring.react.selenium_person-database.git front-end'
								}
							}
						}
						stage('Compile-Package-Test') {
							steps {
								script {
									dir('./front-end/client') {
										sh "npm install"
										sh "npm start &"
										sh "sleep 8m"
									}
								}
							}
						}
					}
				}
				stage('Integration-Testing') {
					agent {
						docker {
							image 'jamesdbloom/docker-java8-maven:latest'
						}
					}
					stages {
						stage('Set Up') {
							steps {
								script {
									sh 'rm -rf integrations'
								}
							}
						}
						stage('SCM Checkout') {
							steps {
								sh 'git clone https://github.com/nmm131/jenkins.docker.spring.react.selenium_person-database integrations'
							}
						}
						stage('Compile-Package-Test') {
							steps {
								script {
									dir('./integrations/integration-testing') {
									    sh "sleep 6m"
										sh "mvn package -Dmaven.test.failure.ignore=true"
									}
								}
							}
						}
					}
				}
			}
		}
	}
}
