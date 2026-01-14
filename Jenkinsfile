pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Application') {
            steps {
                script {
                    // For Maven project
                    sh 'mvn clean install'
                    
                    // Or for Gradle
                    // sh './gradlew build'
                    
                    // Or for Node.js
                    // sh 'npm install'
                    // sh 'npm run build'
                }
            }
        }
        
        stage('Deploy Application') {
            steps {
                script {
                    sh 'echo "Deploying application..."'
                    // Add your deployment commands
                }
            }
        }
        
        stage('Run JMeter Tests') {
            steps {
                script {
                    sh '''
                        /path/to/jmeter/bin/jmeter -n \
                        -t tests/your-test-plan.jmx \
                        -l results/results.jtl \
                        -j logs/jmeter.log
                    '''
                }
            }
        }
        
        stage('Publish Results') {
            steps {
                archiveArtifacts artifacts: 'results/*.jtl', fingerprint: true
                perfReport sourceDataFiles: 'results/*.jtl'
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
