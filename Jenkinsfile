config {
  // How many days old builds should be kept in Jenkins
  daysToKeep  = 21

  // Bouw deze docker container buiten elke git push ook
  // elk weekend. Dit doe je om eventuele fixes in bovenliggende
  // docker images ook mee te krijgen in je eigen build.
  cronTrigger = '@weekend'
}

node() {
  catchError {
    git.checkout { }

    // PowerDNS version
    def versions = readProperties file: "version.properties"
    def pdnsVersion = versions.POWERDNS_VERSION
    def pdnsMajor = semver.getMajor("strict", pdnsVersion)
    def pdnsMinor = semver.getMinor("strict", pdnsVersion)

    // Variabelen.
    def dockerContainerName = "topicuseducation/powerdns"
    def dockerTags = [ ]
    def suffix = '-dev'
    def dockerDistribute = false
    if (env.BRANCH_NAME == "master" || env.BRANCH_NAME == "main") {
        suffix = ''
        dockerDistribute = true
    }

    dockerTags.push("${pdnsVersion}${suffix}")
    dockerTags.push("${pdnsMajor}.${pdnsMinor}${suffix}")

    //dockerfile.validate {}

    // Bouw de Dockerfile en return een img object.
    def img = dockerfile.build {
      name = dockerContainerName
      args = "--build-arg=\"POWERDNS_VERSION=${pdnsVersion}\""
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
}
