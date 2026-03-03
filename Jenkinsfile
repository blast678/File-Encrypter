// This directs the pipeline to run on your newly created 'docker-agent'
node('agent-node') { 
    try { 
        stage('Verify Node Identity') {
            sh '''
            echo "--- DISTRIBUTED BUILD VERIFICATION ---"
            echo "Running on Agent: $NODE_NAME"
            echo "Hostname: $(hostname)"
            echo "User: $(whoami)"
            echo "--- ---"
            '''
        }

        stage('Checkout') {
            // This pulls your latest code from GitHub onto the Agent container
            checkout scm
        }

        stage('Build') { 
            sh '''
            echo "--- COMPILING SOURCE CODE ---"
            # Find Java files in 'src' and wrap in quotes to handle the 'Password Protection' folder space
            find . -path "*/src/*.java" -printf '"%p"\\n' > sources.txt
            
            echo "Source files found:"
            cat sources.txt
            
            mkdir -p build
            javac -d build @sources.txt
            echo "Build successful on Agent!"
            '''
        }

        stage('Test') {
            sh '''
            echo "--- COMPILING & RUNNING TESTS ---"
            # Download JUnit if not present on the agent
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            # Find Java files in 'test' and wrap in quotes
            find . -path "*/test/*.java" -printf '"%p"\\n' > test_sources.txt
            
            echo "Test files found:"
            cat test_sources.txt

            # Compile tests using 'build' classes and junit jar
            javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
            
            echo "Executing Tests..."
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            '''
        }

        stage('Deploy') {
            sh '''
            echo "--- PACKAGING ARTIFACT ---"
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created: FileEncrypter.jar"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
