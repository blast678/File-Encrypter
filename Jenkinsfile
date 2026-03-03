node { 
    try { 
        stage('Build') { 
            sh '''
            echo "Current Directory Content:"
            ls -F
            
            # Use a wildcard to enter the folder to avoid space issues
            cd "Password Protection"
            
            echo "Inside folder. Compiling..."
            mkdir -p build
            javac -d build src/*.java
            echo "Build successful"
            '''
        }

        stage('Test') {
            sh '''
            cd "Password Protection"
            
            if [ ! -f junit-platform-console-standalone.jar ]; then
                echo "Downloading JUnit..."
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java
            
            echo "Running tests..."
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            '''
        }

        stage('Deploy') {
            sh '''
            cd "Password Protection"
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created: FileEncrypter.jar"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
