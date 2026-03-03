node { 
    try { 
        stage('Build') { 
            sh '''
            echo "--- WSL-DOCKER PATH SYNC ---"
            # Find all java files regardless of spaces in folder names
            find . -name "*.java" > sources.txt
            
            echo "Compiling the following files:"
            cat sources.txt
            
            mkdir -p build
            # Compile using the list file to avoid 'cd' errors
            javac -d build @sources.txt
            echo "Build successful!"
            '''
        }

        stage('Test') {
            sh '''
            echo "--- DOWNLOADING JUNIT ---"
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            # Find tests specifically
            find . -path "*/test/*.java" > test_sources.txt
            
            echo "Compiling tests..."
            javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
            
            echo "Executing JUnit tests..."
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            '''
        }

        stage('Deploy') {
            sh '''
            echo "--- PACKAGING JAR ---"
            jar cf FileEncrypter.jar -C build .
            echo "Success: FileEncrypter.jar is ready."
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
