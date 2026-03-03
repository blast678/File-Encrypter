node { 
    try { 
        stage('Build') { 
            sh '''
            echo "Listing files to verify path:"
            ls -F
            
            # Use quotes because of the space in the folder name
            cd "Password Protection"
            
            mkdir -p build
            javac -d build src/*.java
            echo "Build successful"
            '''
        }

        stage('Test') {
            sh '''
            cd "Password Protection"
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
            cd "Password Protection"
            jar cf FileEncrypter.jar -C build .
            '''
        }
    } catch (Exception e) {
        echo "Pipeline failed! Error: ${e}"
        throw e
    }
}
