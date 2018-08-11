node('jenkins-docker-slave') {
    
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "registry.sensa.net:5000/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Pull"

      checkout scm
      sh "git rev-parse --short HEAD > commit-id"

    stage "Build"

        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"

    stage "Push"

        sh "docker push ${imageName}"

}
node('jenkins-kube-slave') {

    stage "Deploy"

        sh "sed 's#registry.sensa.net:5000/hello-kenzan:latest#'$BUILDIMG'#' applications/hello-kenzan/k8s/deployment.yaml | kubectl apply -f -"
        sh "kubectl rollout status deployment/hello-kenzan"

}

