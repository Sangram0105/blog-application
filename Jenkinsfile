pipeline {
    agent any
    tools {
        jdk "jdk"
        maven "maven"
        dockerTool "docker"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Sangram0105/blog-application.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app -Dsonar.projectKey=Blogging-app \
                          -Dsonar.java.binaries=target'''
                }
            }
        }
        stage('Build') {
            steps {
                sh "mvn package"
            }
        }
        stage('Docker Build & Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker build -t sdsankpal7812/gab-blogging-app ."
                    }
                }
            }
        }
        stage('Docker Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker push sdsankpal7812/gab-blogging-app"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                pipelineStatus = pipelineStatus.toUpperCase()

                def bannerColor = pipelineStatus == 'SUCCESS' ? 'green' : 'red'

                def body = """
                <body>
                    <div style="border: 2px solid ${bannerColor}; padding: 10px;">
                        <h3 style="color: ${bannerColor};">
                            Pipeline Status: ${pipelineStatus}
                        </h3>
                        <p>Job: ${jobName}</p>
                        <p>Build Number: ${buildNumber}</p>
                        <p>Status: ${pipelineStatus}</p>
                    </div>
                </body>
                """

                emailext(
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus}",
                    body: body,
                    to: 'sangramsankpal7812@gmail.com',
                    from: 'jenkins@example.com',
                    replyTo: 'jenkins@example.com',
                    mimeType: 'text/html'
                )
            }
        }
    }
}
