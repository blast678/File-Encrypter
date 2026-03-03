node { 
    try { 
        stage('Build') { 
            sh '''
            # This line finds any directory containing 'Password' and 'Protection'
            TARGET_DIR=$(find . -maxdepth 1 -type d -name "*Password*Protection*" | head -n 1)
            echo "Moving into: $TARGET_DIR"
            cd "$TARGET_DIR"
            
            mkdir -p build
            javac -d build src/*.java
            echo "Build successful"
            '''
        }

        stage('Test') {
            sh '''
            TARGET_DIR=$(find . -maxdepth 1 -type d -name "*Password*Protection*" | head -n 1)
            cd "$TARGET_DIR"
            
            if [ ! -f junit-platform-console-standalone.jar ]; then
                curl -L -o junit-platform-console-standalone.jar https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
            fi
            
            mkdir -p test-build
            javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java
            java -jar junit-platform-console-standalone.jar --class-path build:test-build --scan-class-path
            '''
        }

        stage('Deploy') {
            sh '''
            TARGET_DIR=$(find . -maxdepth 1 -type d -name "*Password*Protection*" | head -n 1)
            cd "$TARGET_DIR"
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created successfully"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
