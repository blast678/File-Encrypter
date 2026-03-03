node { 
    try { 
        stage('Build') { 
            sh '''
            echo "Locating source files..."
            # Find the directory that contains the 'src' folder
            SRC_PATH=$(find . -name "src" -type d | head -n 1)
            BASE_DIR=$(dirname "$SRC_PATH")
            
            echo "Project root found at: $BASE_DIR"
            cd "$BASE_DIR"
            
            mkdir -p build
            javac -d build src/*.java
            echo "Build successful"
            '''
        }

        stage('Test') {
            sh '''
            SRC_PATH=$(find . -name "src" -type d | head -n 1)
            BASE_DIR=$(dirname "$SRC_PATH")
            cd "$BASE_DIR"
            
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
            SRC_PATH=$(find . -name "src" -type d | head -n 1)
            BASE_DIR=$(dirname "$SRC_PATH")
            cd "$BASE_DIR"
            
            jar cf FileEncrypter.jar -C build .
            echo "Artifact Created successfully"
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
