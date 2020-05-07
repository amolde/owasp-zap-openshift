#!groovy
def reportDir = "/zap/wrk"
def reportFile = "baseline.html"

// shared between both containers
def workspaceDir = "/tmp/wrk"

stage('Initial setup') {
    properties([
        parameters([
            string(
                name: 'TARGET',
                description: 'Target url',
                defaultValue: 'http://juice-shop:80'
            ),
            string(
                name: 'MINUTES',
                description: 'The number of minutes to spider for',
                defaultValue: '1'
            ),
        ])
    ])
}

stage('Clean Workspace') {
    node('zap') {
        sh "rm -rf $workspaceDir || true"
        sh "mkdir -p $workspaceDir"
    }
}

stage('Scan Web Application') {
    node('zap') {
    container('zap') {
        def tempDir = sh(returnStdout: true, script: "mktemp -d")
        dir(tempDir) {
            def retVal = sh(returnStatus: true, script: "/zap/zap-baseline.py -m $MINUTES -r $reportFile -t $TARGET")
            echo "Return value is: ${retVal}"

            // Share the report
            sh "cp $reportDir/$reportFile $workspaceDir"
        }
    }
    container('jnlp') {
        publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: workspaceDir,
                reportFiles: reportFile,
                reportName: 'ZAP Baseline Scan',
                reportTitles: 'ZAP Baseline Scan'
        ])
        }
    }
}

/*
 * ZAP image
 *
 * Options:
 * - daemon mode
 * - baseline check
 *
 */
