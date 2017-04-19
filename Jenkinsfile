node {
    git 'https://github.com/olexade/todobackend.git'
    
    try {
        stage 'make test'
        sh 'make test'
        
        stage 'make build'
        sh 'make build'
        
        stage 'make release'
        sh 'make release'
        
        stage 'make tag and publish'
        sh "make tag latest \$(git rev-parse --short HEAD) \$(git tag --points-at HEAD)"
        sh "make buildtag master \$(git tag --points-at HEAD)"
        withEnv(["DOCKER_USER=${DOCKER_USER}",
                    "DOCKER_PASSWORD=${DOCKER_PASSWORD}",
                    "DOCKER_EMAIL=${DOCKER_EMAIL}"]) {
                sh "make login"
        }
        sh "make publish"
        
        stage "Deploy release"
        sh "printf \$(git rev-parse --short HEAD) > tag.tmp"
        def imageTag = readFile 'tag.tmp'
        build job: DEPLOY_JOB, parameters: [[
           $class: 'StringParameterValue',
           name: 'IMAGE_TAG',
           value: 'jmenga3/todobackend:' + imageTag
        ]]
    }
    finally {
        stage 'Collect test reports'
        step([$class: 'JUnitResultArchiver', testResults: '**/reports/*.xml'])
        
        stage 'Clean up'
        sh 'make clean'
        sh 'make logout'
    }
}
