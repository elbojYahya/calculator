pipeline {
agent any
    triggers {
        pollSCM('* * * * *')
    }


stages {

stage("Compile") {
steps {
sh "./gradlew compileJava"
}
}

stage("Unit test") {
steps {
sh "./gradlew test"
}
}
 
stage("Code coverage") {
steps {
sh "./gradlew jacocoTestReport"
publishHTML (target: [
reportDir: 'build/reports/jacoco/test/html',
reportFiles: 'index.html',
reportName: "JaCoCo Report"
])
sh "./gradlew jacocoTestCoverageVerification"
}
}

stage("Static code analysis") {
steps {
sh "./gradlew checkstyleMain"
publishHTML (target: [
reportDir: 'build/reports/checkstyle/',
reportFiles: 'main.html',
reportName: "Checkstyle Report"
])
}
}

stage("Package") {
steps {
sh "./gradlew build"
}
}

stage("Docker build") {
steps {
sh "docker build -t elboj/calculator ."
}
}

stage("Docker login") { steps { sh "docker login --username elboj --password yahyar13boj"  } }
stage("Docker push") {
steps {
sh "docker push elboj/calculator"
}
}

stage("Deploy to staging") { 
steps{ sh "docker run -d --rm -p 8090:8090 --name calculator elboj/calculator" }
}

stage("Acceptance test") {
steps { sleep 30
	sh "chmod +x calculator_test.sh && ./calculator_test.sh" }
}
}
post { always { sh "docker stop calculator" } }

}

