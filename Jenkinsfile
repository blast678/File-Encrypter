node { 
    try { 
        stage('Checkout') {
            checkout scm
        }

        stage('Build') { 
            sh '''
            echo "--- COMPILING SOURCE CODE ---"
            # Only find java files inside the 'src' folder
            find . -path "*/src/*.java" -printf '"%p"\\n' > sources.txt
            
            echo "Source files found:"
            cat sources.txt
            
            mkdir -p build
            javac -d build @sources.txt
            echo "Build successful!"
            '''
        }

        stage('Test') {
            sh '''
            echo "--- COMPILING & RUNNING TESTS ---"
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            # Only find java files inside the 'test' folder
            find . -path "*/test/*.java" -printf '"%p"\\n' > test_sources.txt
            
            echo "Test files found:"
            cat test_sources.txt

            # Compile tests using 'build' (app classes) and 'junit' jar
            javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
            
            echo "Executing Tests..."
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
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
