// Define jobName here
def jobName = env.JOB_NAME

pipeline {
    agent any
    environment {
        inputdata = '' // Define inputdata at the pipeline level
        carbonAppName = 'SuccessSampleGuarantyDelivaryCompositeExporter'
        customJobName = "${jobName}"
                    

    }
    options {         disableConcurrentBuilds()     }
    stages {
        
        stage('Call Management API') { // A single stage that encompasses both steps
            steps {
                script {
                   
                    echo "Current Job Name: ${jobName}"
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

                    // //if (SecondstatusCode == 200) {
                    //     def jsonResponseSecond = new groovy.json.JsonSlurper().parseText(SecondresponseBody)
                    //     echo "Parsed JSON Response Second: ${jsonResponseSecond}"
                        
                    // } else {
                    //     echo "Second endpoint request  with status code ${SecondstatusCode}"
                    // }


                    if (SecondstatusCode != 200 && SecondstatusCode != 401) {
                        // Step 2: Call the third Endpoint to get the number of Carfiles Deployed
                                echo "AccessTokenFirst: ${inputdata}"
                                def resthree = httpRequest(
                                    url: 'https://localhost:9164/management/applications',
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


                    } else 
                        if (SecondstatusCode == 200) {
                         def jsonResponseSecond = new groovy.json.JsonSlurper().parseText(SecondresponseBody)
                        echo "Parsed JSON Response Second: ${jsonResponseSecond}"

                            }
                            else {
                                echo "Second endpoint request  with status code ${SecondstatusCode}"
                            }
            }
        }
    } 
    
// stage to Check Current Build Status

stages {

        stage('Trigger Last Successful Build') {

            steps {

                script {

                    def projectToTrigger = ${jobName}
                    echo "job name ${SecondstatusCode}"
                    def lastSuccessfulBuild = jenkins.model.Jenkins.instance.getItem(projectToTrigger).getLastSuccessfulBuild()
                    echo "Last successful build  ${SecondstatusCode}"
                    if (lastSuccessfulBuild) {

                        build(job: projectToTrigger, parameters: [], propagate: false)

                    } else {

                        error("No successful builds found for ${projectToTrigger}")

                    }

                }

            }

        }

    }


        }

}
    
            
        

