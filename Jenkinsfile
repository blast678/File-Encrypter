node { 
    try { 
        stage('Checkout') {
            // Use double slashes for comments outside of sh blocks
            checkout scm
        }

        stage('Build') { 
            sh '''
            echo "Listing all files in workspace:"
            ls -R
            
            # Inside sh blocks, # is the correct comment character
            find . -name "*.java" > sources.txt
            
            if [ ! -s sources.txt ]; then
                echo "ERROR: No .java files found!"
                exit 1
            fi
            
            echo "Files found for compilation:"
            cat sources.txt
            
            mkdir -p build
            javac -d build @sources.txt
            echo "Build successful!"
            '''
        }

        stage('Test') {
            sh '''
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            find . -path "*/test/*.java" > test_sources.txt
            
            if [ -s test_sources.txt ]; then
                javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
                java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            else
                echo "No tests found to run."
            fi
            '''
        }

        stage('Deploy') {
            sh '''
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created successfully"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
