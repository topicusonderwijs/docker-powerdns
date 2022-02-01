config {
    // How many days old builds should be kept in Jenkins
    daysToKeep  = 21
    // Bouw deze docker container buiten elke git push ook
    // elk weekend. Dit doe je om eventuele fixes in bovenliggende
    // docker images ook mee te krijgen in je eigen build.
    cronTrigger = '@weekend'
}

node(){
  catchError {

    // Variabelen.
    def dockerContainerName = "topicuseducation/powerdns"
    def dockerTags = [ ]
    def suffix = '-dev'
    def dockerDistribute = false
    if (env.BRANCH_NAME == "master" || env.BRANCH_NAME == "main") {
        suffix = ''
        dockerDistribute = true
    }

    // Checkout de git repository
    git.checkout { }

    dockerTags.push("4.6.0" + suffix)
    dockerTags.push("4.6" + suffix)

    dockerfile.validate {}

    // Bouw de Dockerfile en return een img object.
    def img = dockerfile.build {
        name = dockerContainerName
    }

    if (env.BRANCH_NAME == "master") {

      dockerfile.publish {
          image = img
          latestTag = dockerDistribute
          tags = dockerTags
          distribute = dockerDistribute
      }

    }

}
