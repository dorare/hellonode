#!groovy

/*
 This is the Duara Systems Jenkins file that will pull from the repository, test the scripts for any error - build an image and deploy the image to the google docker registries upon completion
*/

node {


    currentBuild.result = "SUCCESS"
    def dockerfile = "${currentBuild.displayName}_dockerfile"
    def imageName = "${currentBuild.displayName}_image"
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

        stage('Build Test') {
            print "Environment will be : ${env.NODE_ENV}"
            print "Running docker tests"
            //Add tests
            app = docker.build(imageName, "-f ${dockerfile} ./path/to/file")
            //This will assign the repo name as the build name

        }

        stage('Test') {
            print "Environment will be : ${env.NODE_ENV}"
            print "Running docker tests"
            // TO DD - Add docker test here
            app.inside {
                sh 'make test'
            }

        }

        stage('Deploy') {

            echo 'Pushing Docker Image to Repo'
            /* Finally, we'll push the image with two tags:
             * First, the incremental build number from Jenkins
             * Second, the 'latest' tag.
             * Pushing multiple tags is cheap, as all the layers are reused. */
            //Add the google registry here
            docker.withRegistry('', 'google-registry-credentials') {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")

            }
        }

            stage('Cleanup') {

                sh "docker rmi ${imageName}"
                // Add mailing details here
                mail body: 'Project build successful',
                        from: 'xxxx@yyyyy.com',
                        replyTo: 'xxxx@yyyy.com',
                        subject: 'project build successful',
                        to: 'yyyyy@yyyy.com'
            }


        }
        catch (err) {

            currentBuild.result = "FAILURE"
            mail body: "project build error is here: ${env.BUILD_URL}",
                    from: 'xxxx@yyyy.com',
                    replyTo: 'yyyy@yyyy.com',
                    subject: 'project build failed',
                    to: 'zzzz@yyyyy.com'

            throw err
        }

    }
