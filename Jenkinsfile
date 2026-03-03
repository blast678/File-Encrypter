node { 
    try { 
        stage('Checkout') {
            checkout scm
        }

        stage('Build') { 
            sh '''
            echo "--- SEARCHING FOR SOURCE FILES ---"
            # Find all .java files and wrap them in quotes to handle spaces
            find . -name "*.java" -printf '"%p"\\n' > sources.txt
            
            echo "Files found (quoted):"
            cat sources.txt
            
            mkdir -p build
            # Compile using the quoted list
            javac -d build @sources.txt
            echo "Build successful!"
            '''
        }

        stage('Test') {
            sh '''
            echo "--- RUNNING TESTS ---"
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            # Find tests and wrap them in quotes
            find . -path "*/test/*.java" -printf '"%p"\\n' > test_sources.txt
            
            if [ -s test_sources.txt ]; then
                javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
                java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            else
                echo "No tests found."
            fi
            '''
        }

        stage('Deploy') {
            sh '''
            echo "--- PACKAGING ---"
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created: FileEncrypter.jar"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
