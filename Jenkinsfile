import com.fairstone.Utils
import com.fairstone.AWSUtils

def call(body) {
    def config = [:]
    body.resolveStrategy = Closure.DELEGATE_FIRST
    body.delegate = config
    body()

    def linuxAgent = "slaves"
    def ssisBuildAgent = "ssis-build"

    def ssisClientVersion = config.ssisClientVersion ?: "ssis-client 0.10.0"
    def usedJdk = config.jdk ?: "ibm-jdk8"
    def usedMavenSettings = config.mavenSettings ?: "fairstone-maven-settings"
    def usedMaven = config.maven ?: "maven-3.5"
    def deploy = config.deploy ?: false
    def ssisArtifact = config.ssisArtifact ?: ""
    def ssisConfigVersion = config.ssisConfigVersion ?: "1.0"
    def mailTo = config.mailTo ?: com.fairstone.Utils.DEFAULT_MAIL_TO_BUILD
    def unitTests = config.unitTests ?: false

    def featurePattern = java.util.regex.Pattern.compile("(origin/)?feature/(.*)")
    def developPattern = java.util.regex.Pattern.compile("(origin/)?develop")

    def customVersion = currentBuild.startTimeInMillis.toString()

    properties(
        [
            buildDiscarder(logRotator(numToKeepStr: '15')),
            pipelineTriggers([pollSCM('*/5 * * * *')]),
        ]
    )
    def pom = null

    try
    {
        timeout(time: 25, unit: 'MINUTES') {

            def pipeline = [:]
            
            if(ssisArtifact != ""){
                pipeline['SSIS'] = {
                    node (ssisBuildAgent){
                        stage('git clone') {
                            deleteDir()
                            checkout([
                                    $class           : 'GitSCM',
                                    branches         : scm.branches,
                                    extensions       : scm.extensions + [[$class: 'CleanCheckout'], [$class: 'LocalBranch']],
                                    userRemoteConfigs: scm.userRemoteConfigs
                            ])
                        }
                        stage('ssis build') {
                            script {
                                def lpom = readMavenPom file: 'pom.xml'
                                def groupID = config.groupID ?: "com.fairstone"
                                def ssisClient = "${tool ssisClientVersion}"
                                def cmd = "${ssisClient}\\Build\\All.ps1 -groupID ${groupID} -artifactID ${ssisArtifact} -version ${lpom.version} -customVersion ${customVersion}"
                                if (!featurePattern.matcher(env.BRANCH_NAME).matches()){
                                    cmd = cmd + " -Deploy"
                                }
                                bat 'powershell.exe -NonInteractive -NoProfile -ExecutionPolicy Bypass -Command "' + cmd + '"'

                            }
                        }
                    }
                }
            }
            pipeline['Maven'] = {
                node (linuxAgent) {

                    stage('git clone') {
                        deleteDir()
                        checkout([
                                $class           : 'GitSCM',
                                branches         : scm.branches,
                                extensions       : scm.extensions + [[$class: 'CleanCheckout'], [$class: 'LocalBranch']],
                                userRemoteConfigs: scm.userRemoteConfigs
                        ])
                        script {
                                pom = readMavenPom file: 'pom.xml'
                                VERSION = pom.version.update('SNAPSHOT', BUILDNUMBER + "." + SHORTREV)
                        }
                    }

                    stage('mvn verify') {
                        echo "============== ${env.BRANCH_NAME} ================"

                        withMaven(
                                maven: usedMaven,
                                jdk: usedJdk,
                                mavenSettingsConfig: usedMavenSettings) {
                            script {
                                if (unitTests) {
                                    sh "mvn clean test -U"
                                } else {
                                    sh "mvn clean verify -U"
                                }
                            }
                        }
                    }

                    if(!featurePattern.matcher(env.BRANCH_NAME).matches()) {
                        //If Feature => no publish in nexus no deploy
                        stage('publish nexus java') {                            
                            withMaven(
                                    maven: usedMaven,
                                    jdk: usedJdk,
                                    mavenSettingsConfig: usedMavenSettings) {

                                sh "mvn clean deploy -U"
                            }
                        }
                    }
                }
            }
            parallel pipeline

            if(deploy && developPattern.matcher(env.BRANCH_NAME).matches()) {
                stage('deploy in development') {
                    script {
                        def groupID = config.groupID ?: "com.fairstone"
                        def jobDeploy = config.jobDeploy ?: "../dist-apps-java-pipeline-deploy-job/deployment-java-dists-app"
                        def artifactID = config.artifactID ?: pom.artifactId
                        def packaging = config.packaging ?: pom.packaging
                        def deployment = config.deployment ?: ""
                        def environment = config.environment ?: Utils.ENVIRONMENT_DEV
                        def stack = config.stack ?: AWSUtils.DEFAULT_STACK
                        def stackId = config.stackId ?: AWSUtils.DEFAULT_STACK_ID

                        echo "Deploy in environment"
                        build job: jobDeploy, wait: false,
                                parameters: [[$class: 'StringParameterValue', name: 'groupID', value: groupID, description: 'The groupID of artifact'],
                                                [$class: 'StringParameterValue', name: 'artifactID', value: artifactID, description: 'The artifactID of artifact'],
                                                [$class: 'StringParameterValue', name: 'packaging', value: packaging, description: 'The packaging of artifact'],
                                                [$class: 'StringParameterValue', name: 'version', value: pom.version, description: 'The version of artifact'],
                                                [$class: 'StringParameterValue', name: 'deployment', value: deployment, description: 'The information of deployment'],
                                                [$class: 'StringParameterValue', name: 'environment', value: environment, description: 'The environment'],
                                                [$class: 'StringParameterValue', name: 'stack', value: stack, description: 'The kind of stack'],
                                                [$class: 'StringParameterValue', name: 'stackId', value: stackId, description: 'The stack id'],
                                                [$class: 'StringParameterValue', name: 'mailTo', value: mailTo, description: 'The mail to']]
                        if (ssisArtifact != "") {
                            def jobDeploySsis = config.jobDeploySsis ?: "../dist-apps-java-pipeline-deploy-job/deployment-ssis"
                            build job: jobDeploySsis, wait: false,
                                    parameters: [[$class: 'StringParameterValue', name: 'groupID', value: groupID, description: 'The groupID of artifact'],
                                                    [$class: 'StringParameterValue', name: 'artifactID', value: ssisArtifact, description: 'The artifactID of artifact'],
                                                    [$class: 'StringParameterValue', name: 'version', value: pom.version, description: 'The version of artifact'],
                                                    [$class: 'StringParameterValue', name: 'customVersion', value: customVersion, description: 'The custom version'],
                                                    [$class: 'StringParameterValue', name: 'ssisConfigVersion', value: ssisConfigVersion, description: 'The configuration version'],
                                                    [$class: 'StringParameterValue', name: 'environment', value: environment, description: 'The environment'],
                                                    [$class: 'StringParameterValue', name: 'stackId', value: stackId, description: 'The stack id'],
                                                    [$class: 'StringParameterValue', name: 'mailTo', value: mailTo, description: 'The mail to']]
                        }
                    }
                }
            }
        }
    }
    catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    }
    finally{
        stage('Cleanup'){

            if(ssisArtifact != ""){
                node (ssisBuildAgent){
                    deleteDir()
                }
            }
            node (linuxAgent){
                deleteDir()
            }
            
            mail to: mailTo,
                    subject: "[FAIRSTONE-JENKINS][BUILD][${currentBuild.currentResult}] - ${currentBuild.fullDisplayName}",
                    body: """
                    Informations:
                    <ul>
                        <li><a href=\"${env.BUILD_URL}\">Job</a></li>
                        <li>Status  <b>${currentBuild.currentResult}</b></li>
                        <li>Display Name  <b>${currentBuild.displayName}</b></li>
                        <li>Job Name <b>${env.JOB_NAME}</b></li>
                        <li>Build Number <b>${env.BUILD_NUMBER}</b></li>
                        <li>Git branch <b>${env.GIT_BRANCH}</b></li>
                        <li>Git commit <b>${env.GIT_COMMIT}</b></li>
                        <li>Duration ${currentBuild.durationString}</li>
                    </ul>
                    """,
                    mimeType: "text/html"
        
        }
    }
}
