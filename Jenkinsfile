pipeline {
    agent any 

    tools {
        maven 'Maven 3.5.2'
        jdk 'Java 7'
    }

//    environment {
//	BITBUCKET_COMMON_CREDS = credentials('jenkins-bitbucket-common-creds')
//    }

    parameters {
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/colosull/test.git', description: 'The Git repo to use')
        string(name: 'BRANCH', defaultValue: 'test-2', description: 'The Git branch name to build')
	string(name: 'EMAIL_TO', defaultValue: 'colosull@gmail.com', description: 'Build failures will be sent to this address')
    }


    // Create an Artifactory server instance, as described above in this article:
//    def server = Artifactory.server('my-server-id')
    // Create and set an Artifactory Maven Build instance:
//    def rtMaven = Artifactory.newMavenBuild()
//    rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
//    rtMaven.resolver server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'
    // Optionally include or exclude artifacts to be deployed to Artifactory:
//    rtMaven.deployer.artifactDeploymentPatterns.addInclude("frog*").addExclude("*.zip")
    // Set a Maven defined in Jenkins "Manage":
//    rtMaven.tool = MAVEN_TOOL
    // Optionally set Maven Ops
//    rtMaven.opts = '-Xms1024m -Xmx4096m'
    // Run Maven:
//    def buildInfo = rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install'
    // Alternatively, you can pass an existing build-info instance to the run method.
    // This way, you can merge multiple build-info instances into one instance (using the buildInfo.append method),
    // as described above in this article:
//    rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install', buildInfo: buildInfo
    // Publish the build-info to Artifactory:
//    server.publishBuildInfo buildInfo


    stages {

	stage('Git checkout') {
	    steps {
		echo 'Checkout from Git repo...'
		git url: "${params.GIT_REPO}", branch: "${params.BRANCH}"
	    }
	}

	stage('Merge') {
	    steps {
		echo 'Merging from master...'
		sh 'git tag -a tagName -m "Jenkins build #${env.BUILD_ID}"'
		sh 'git merge origin/master'
		sh 'git commit -am "Merged master branch to ${params.BRANCH}"'
		sh "git push"
	    }
	}

	stage('Build') {
            steps {
		echo "Building ${env.BRANCH_NAME} ${env.CHANGE_ID}..."
		sh 'mvn clean package'
            }
	}

        stage('Test') { 
            steps {
		echo 'Testing...'
            }
        }

        stage('Deploy') { 
	    when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
		echo 'Deploying...'
            }
        }

        stage('Report') { 
            steps {
		echo "Finished build id ${env.BUILD_ID}"
		echo "Build Name/Result ${currentBuild.displayName}/${currentBuild.result}" 
            }
        }
    }
    post {
        failure {
	    mail to: "${params.EMAIL_TO}", bcc: '', cc: '', from: '', replyTo: '', 
		subject: "Pipeline failed ${currentBuild.displayName}#${env.BUILD_ID}", 
		body: "Pipeline failed: ${currentBuild.result}. ${currentBuild.displayName}#${env.BUILD_ID}"
	    //slack notify???
        }
    }
}
