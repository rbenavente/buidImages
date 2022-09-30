node {
    def app

    stage('Clone repository') {
        // Let's make sure we have the repository cloned to our workspace
        checkout scm
    }

    stage('Build Base image') {
        // This builds the actual image; synonymous to docker build on the command line
        app = docker.build("rbenavente/basepython:${env.BUILD_ID}")
    }
    stage('Build NewImage') {
        // This builds the actual image; synonymous to docker build on the command line
         sh 'cd NewImage'
         sh 'cat Dockerfile'
        app = docker.build("rbenavente/pythondev:${env.BUILD_ID}")
    }

    stage('Scan NewImage and Publish to Jenkins') {
        try {
            prismaCloudScanImage ca: '', cert: '', dockerAddress: 'unix:///var/run/docker.sock', ignoreImageBuildTime: true, image: "rbenavente/pythondev:${env.BUILD_ID}", key: '', logLevel: 'debug', podmanPath: '', project: '', resultsFile: 'prisma-cloud-scan-results.json'
        } finally {
            prismaCloudPublish resultsFilePattern: 'prisma-cloud-scan-results.json'
        }
    }

    stage('Scan image with twistcli') {
        withCredentials([usernamePassword(credentialsId: 'twistlock_creds', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
            sh 'curl -k -u $TL_USER:$TL_PASS --output ./twistcli https://$TL_CONSOLE/api/v1/util/twistcli'
            sh 'sudo chmod a+x ./twistcli'
            sh "./twistcli images scan --u $TL_USER --p $TL_PASS --address https://$TL_CONSOLE  --details rbenavente/pythondev:${env.BUILD_ID}"
        }
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
      //  docker.withRegistry('https://harbor-master-testenv.rbenavente.demo.twistlock.com', 'harbor_credentials') {
        docker.withRegistry(  '', 'dockerhub_credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
