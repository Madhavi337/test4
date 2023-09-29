pipeline {
    agent any
    environment {
        inputdata = '' // Define inputdata at the pipeline level
        carbonAppName = 'SuccessSampleGuarantyDelivaryCompositeExporter'
    }

    stages {
        stage('Call Management API') {
            steps {
                script {
                    try {
                        // Step 1: Call the First Endpoint for Access Token
                        def response = httpRequest(
                            url: 'https://localhost:9164/management/login',
                            httpMode: 'GET',
                            customHeaders: [[name: "Authorization", value: "Basic YWRtaW46YWRtaW4="]],
                            acceptType: 'APPLICATION_JSON',
                            responseHandle: 'NONE',
                            timeout: 60,
                            validResponseCodes: '200',
                            ignoreSslErrors: true,
                        )

                        // Capture the response status code and content
                        def statusCode = response.getStatus()
                        def responseBody = response.getContent()

                        echo "Response Status Code: ${statusCode}"
                        echo "Response Body: ${responseBody}"

                        if (statusCode == 200) {
                            def jsonResponseFirst = new groovy.json.JsonSlurper().parseText(responseBody)
                            echo "Parsed JSON Response First: ${jsonResponseFirst}"
                            inputdata = jsonResponseFirst.AccessToken // Assign inputdata at the pipeline level
                            echo "AccessTokenFirst: ${inputdata}"
                        } else {
                            error("First endpoint request failed with status code ${statusCode}")
                        }

                        // Step 2: Call the Second Endpoint to get no of Carfiles Deployed
                        echo "AccessTokenFirst: ${inputdata}"
                        def res = httpRequest(
                            url: "https://localhost:9164/management/applications?carbonAppName=${carbonAppName}",
                            httpMode: 'GET',
                            customHeaders: [[name: "Authorization", value: "Bearer ${inputdata}"]],
                            acceptType: 'APPLICATION_JSON',
                            responseHandle: 'NONE',
                            timeout: 60,
                            validResponseCodes: '200,404', // Include 404 as an accepted status code
                            ignoreSslErrors: true,
                        )

                        // Capture the response status code and content
                        def SecondstatusCode = res.getStatus()
                        def SecondresponseBody = res.getContent()

                        echo "Response Status Code: ${SecondstatusCode}"
                        echo "Response Body: ${SecondresponseBody}"

                        if (SecondstatusCode != 200 && SecondstatusCode != 401) {
                            // Step 2: Call the third Endpoint to get the number of Carfiles Deployed
                            echo "AccessTokenFirst: ${inputdata}"
                            def resthree = httpRequest(
                                url: '',
                                httpMode: 'GET',
                                customHeaders: [[name: "Authorization", value: "Bearer ${inputdata}"]],
                                acceptType: 'APPLICATION_JSON',
                                responseHandle: 'NONE',
                                timeout: 60,
                                validResponseCodes: '200',
                                ignoreSslErrors: true,
                            )
                            echo "Second endpoint request failed with status code ${SecondstatusCode}"

                            // Capture the response status code and content
                            def ThirdstatusCode = resthree.getStatus()
                            def ThirdresponseBody = resthree.getContent()

                            echo "Response Status Code: ${ThirdstatusCode}"
                            echo "Response Body: ${ThirdresponseBody}"

                            def YourCarbonAppNameHere = carbonAppName

                            if (ThirdstatusCode == 200) {
                                def jsonResponseThird = new groovy.json.JsonSlurper().parseText(ThirdresponseBody)
                                echo "Parsed JSON Response Third: ${jsonResponseThird}"

                                faultyList = jsonResponseThird.faultyList // Assign inputdata at the pipeline level
                                echo "faultyList: ${jsonResponseThird.faultyList}"

                                if (jsonResponseThird.faultyList) {
                                    def carbonAppNameToCheck = "YourCarbonAppNameHere"

                                    def isCarbonAppNamePresent = jsonResponseThird.faultyList.any { item ->
                                        item.name == carbonAppNameToCheck
                                    }

                                    if (isCarbonAppNamePresent) {
                                        echo "The carbonAppName '${carbonAppNameToCheck}' is present in the response."
                                    } else {
                                        echo "The carbonAppName '${carbonAppNameToCheck}' is not present in the response."
                                    }
                                } else {
                                    echo "No carbonAppName is found in faultyList  response."
                                }
                            } else {
                                error("Third endpoint request failed with status code ${ThirdstatusCode}")
                            }
                        } else {
                            error("Second endpoint request failed with status code ${SecondstatusCode}")
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("An error occurred in the Call Management API stage: ${e.getMessage()}")
                    }
                }
            }
        }

        stage('Check Build Status') {
    steps {
        script {
            def currentBuildStatus = currentBuild.result

            echo "Current Build Result: ${currentBuild.result}"

            if (currentBuildStatus == 'SUCCESS') {
                echo "The current build was successful."
            } else {
                echo "The current build was not successful."

                // Get the upstream cause from the build
                def upstreamCause = currentBuild.rawBuild.getCause(hudson.model.Cause$UpstreamCause)

                if (upstreamCause) {
                    def upstreamProject = upstreamCause.upstreamProject
                    def upstreamBuildNumber = upstreamCause.upstreamBuild

                    echo "Triggered by upstream project '${upstreamProject}' build number ${upstreamBuildNumber}"

                    if (upstreamProject && upstreamBuildNumber) {
                        def lastUnsuccessfulBuild = build(job: upstreamProject, parameters: [[$class: 'RebuildSettings', rebuild: true]], wait: true)
                        if (lastUnsuccessfulBuild.resultIsWorseThan('SUCCESS')) {
                            error "Rebuilding the last unsuccessful build (Build #${lastUnsuccessfulBuild.number}) of ${upstreamProject} failed."
                        } else {
                            echo "Successfully triggered the rebuild of the last unsuccessful build (Build #${lastUnsuccessfulBuild.number}) of ${upstreamProject}."
                        }
                    } else {
                        echo "This build was not triggered by an upstream project or the upstream project/build information is not available."
                    }
                } else {
                    echo "This build was not triggered by an upstream project."
                }
            }
                }
            }
        }
    }
}
