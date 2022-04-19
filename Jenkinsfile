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
            steps {
                nexusPublisher nexusInstanceId: 'local', nexusRepositoryId: 'maven-snapshots', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/nukkit-1.0-SNAPSHOT.jar']], mavenCoordinate: [artifactId: 'nukkit', groupId: 'cn.nukkit', packaging: 'jar', version: '1.0-SNAPSHOT']]]
            }
        }
    }
}