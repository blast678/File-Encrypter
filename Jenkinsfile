node { 
    try { 
        stage('Build') { 
            sh '''
            echo "Searching for Java files..."
            # Find all .java files and save their paths to a file
            find . -name "*.java" > sources.txt
            
            echo "Files found:"
            cat sources.txt
            
            mkdir -p build
            # Compile using the list of files found
            javac -d build @sources.txt
            echo "Build successful"
            '''
        }

        stage('Test') {
            sh '''
            # Download JUnit to the root so we don't have to hunt for it
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            # Find all test files
            find . -path "*/test/*.java" > test_sources.txt
            
            javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
            
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            '''
        }

        stage('Deploy') {
            sh '''
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created: FileEncrypter.jar"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
