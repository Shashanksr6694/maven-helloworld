stage 'Compile'
node('master') {
    def mvnHome = tool 'M3'
    sh "${mvnHome}/bin/mvn clean install -DskipTests"
    stash 'working-copy'
}

stage 'Test'
parallel one: {
    node('master') {
        unstash 'working-copy'
        def mvnHome = tool 'M3'
        sh "${mvnHome}/bin/mvn test -Diterations=10"
    }
}, two: {
    node('master') {
        unstash 'working-copy'
        def mvnHome = tool 'M3'
        sh "${mvnHome}/bin/mvn test -Diterations=5"
    }
}, failFast: true

stage 'Code Quality'
node('master') {
    unstash 'working-copy'
    step([$class: 'CheckStylePublisher'])
    step([$class: 'FindBugsPublisher'])
    step([$class: 'PmdPublisher'])
}

stage name: 'Deploy', concurrency: 1
def path = input message: 'Where should I deploy this build?', parameters: [[$class: 'StringParameterDefinition', name: 'FILE_PATH']]
node('master') {
    unstash 'working-copy'
    sh "cp target/example-1.0-SNAPSHOT.jar ${path}"
}
