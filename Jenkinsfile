pipeline {
    agent any
    tools {
        maven 'Maven 3'
        jdk 'Java 8'
    }
    options {
        buildDiscarder(logRotator(artifactNumToKeepStr: '5'))
    }
    stages {
        stage ('Build') {
            steps {
                sh 'mvn clean package -B'
            }
            post {
                success {
                    junit 'target/surefire-reports/**/*.xml'
                    archiveArtifacts artifacts: 'target/nukkit-*-SNAPSHOT.jar', fingerprint: true
                }
            }
        }

        stage ('Deploy') {
            when {
                branch "master"
            }

            steps {
                rtMavenDeployer (
                        id: "maven-deployer",
                        serverId: "prum-artifactory",
                        releaseRepo: "maven-releases",
                        snapshotRepo: "maven-snapshots"
                )
                rtMavenResolver(
                        id: "maven-resolver",
                        serverId: "prum-artifactory",
                        releaseRepo: "maven-deploy-release",
                        snapshotRepo: "maven-deploy-snapshot"
                )
                rtMavenRun (
                        pom: 'pom.xml',
                        goals: 'javadoc:javadoc javadoc:jar source:jar install -B -DskipTests',
                        deployerId: "maven-deployer",
                        resolverId: "maven-resolver"
                )
                rtPublishBuildInfo (
                        serverId: "prum-artifactory"
                )
                step([$class: 'JavadocArchiver', javadocDir: 'target/site/apidocs', keepAll: false])
            }
        }
    }
}