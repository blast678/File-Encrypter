node { 
    try { 
        stage('Checkout') {
            # This ensures Jenkins actually pulls your code from GitHub first
            checkout scm
        }

        stage('Build') { 
            sh '''
            echo "Checking current directory:"
            ls -R
            
            # Find all java files and verify if any exist
            find . -name "*.java" > sources.txt
            
            if [ ! -s sources.txt ]; then
                echo "ERROR: No .java files found in workspace!"
                exit 1
            fi
            
            echo "Files found:"
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
            javac -cp junit-platform-console-standalone.jar:build -d test-build @test_sources.txt
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            '''
        }

        stage('Deploy') {
            sh '''
            jar cf FileEncrypter.jar -C build .
            echo "Success!"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
