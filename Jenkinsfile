#!groovy

/*
 This is the Duara Systems Jenkins file that will pull from the repository, test the scripts for any error - build an image and deploy the image to the google docker registries upon completion
*/

node {


    currentBuild.result = "SUCCESS"
    def imageName
    def app
    try {
        /*
      Checkout code from the vsts repository
     */
        stage('Checkout') {
            checkout scm
        }
        /*
         Print the environment variables in the Jenkins shell
         */
        stage('Environment') {
            sh 'git --version'
            echo "Branch: ${env.BRANCH_NAME}"
            sh 'docker -v'
            sh 'printenv'
        }
        /*
      Build a docker image from the Dockerfile pulled from the repo
      */

        stage('Build') {
            print "Building docker image..."
            //Add tests
            imageName = 'gcr.io/robotic-fuze-194312/build'
            app = docker.build(imageName)
            //app = docker.build(imageName, "-f ${dockerfile} .")
            //This will assign the repo name as the build name

        }

        stage('Test') {
            print "Environment will be : ${env.NODE_ENV}"
            print "Running docker tests"
            // TO DD - Add docker test here
            app.inside {
                sh 'echo "Tests Completed"' //Replace this with real test when we have tests

            }

        }

        stage('Deploy') {

            echo 'Pushing Docker Image to Repo'
            /* Finally, we'll push the image with two tags:
             * First, the incremental build number from Jenkins
             * Second, the 'latest' tag.
             * Pushing multiple tags is cheap, as all the layers are reused. 
	     */
            docker.withRegistry('https://gcr.io', 'gcr:google_credentials') {
               app.push("${env.BUILD_NUMBER}")
               //app.push("latest")
               

            }
            echo 'Image pushed to repo'
        }

            stage('Cleanup') {

                sh "docker rmi ${imageName}"
                // Add mailing details here
                mail body: 'Project build successful',
                        from: 'Mpangokali@gmail.com',
                        replyTo: 'dorare@duara.io',
                        subject: 'project build successful',
                        to:  'dorare@duara.io'
            }


        }
        catch (err) {

            currentBuild.result = "FAILURE"
            mail body: "project build error is here: ${env.BUILD_URL}",
                    from: 'Mpangokali@gmail.com',
                    replyTo: 'dorare@duara.io',
                    subject: 'project build failed',
                    to: 'dorare@duara.io'

            throw err
        }

    }
