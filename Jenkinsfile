pipeline {
    agent any
    
    environment {
        // Set your JMeter installation path
        JMETER_HOME = 'C:\\jmeter'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '========== Checking out code from GitHub =========='
                checkout scm
                echo 'Checkout completed!'
            }
        }
        
        stage('Verify JMeter Script') {
            steps {
                echo '========== Verifying JMeter script exists =========='
                script {
                    bat '''
                        if exist employee.jmx (
                            echo employee.jmx found!
                        ) else (
                            echo ERROR: employee.jmx not found!
                            exit 1
                        )
                    '''
                }
            }
        }
        
        stage('Run JMeter Tests') {
            steps {
                echo '========== Running JMeter Performance Tests =========='
                script {
                    // Create result directories
                    bat '''
                        if not exist jmeter-results mkdir jmeter-results
                        if not exist jmeter-logs mkdir jmeter-logs
                    '''
                    
                    // Run JMeter test
                    bat """
                        echo Running employee.jmx test...
                        ${JMETER_HOME}\\bin\\jmeter.bat -n ^
                        -t ${WORKSPACE}\\employee.jmx ^
                        -l ${WORKSPACE}\\jmeter-results\\results-${BUILD_NUMBER}.jtl ^
                        -j ${WORKSPACE}\\jmeter-logs\\jmeter-${BUILD_NUMBER}.log ^
                        -e -o ${WORKSPACE}\\jmeter-results\\html-report-${BUILD_NUMBER}
                    """
                    
                    echo 'JMeter tests completed!'
                }
            }
        }
        
        stage('Publish Results') {
            steps {
                echo '========== Publishing Test Results =========='
                
                // Archive the results
                archiveArtifacts artifacts: 'jmeter-results/*.jtl, jmeter-logs/*.log', 
                                 fingerprint: true,
                                 allowEmptyArchive: false
                
                // Publish Performance Report
                perfReport sourceDataFiles: 'jmeter-results/results-*.jtl',
                           errorFailedThreshold: 5,
                           errorUnstableThreshold: 2
                
                // Publish HTML Report
                publishHTML([
                    reportDir: "jmeter-results/html-report-${BUILD_NUMBER}",
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
                
                echo 'Results published successfully!'
            }
        }
    }
    
    post {
        always {
            echo '========== Pipeline Finished =========='
        }
        success {
            echo '✓✓✓ BUILD SUCCESS! JMeter tests passed! ✓✓✓'
        }
        failure {
            echo '✗✗✗ BUILD FAILED! Check console output for errors. ✗✗✗'
        }
    }
}
