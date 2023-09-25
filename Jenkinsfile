pipeline {
    agent any

    environment {
        inputdata = '' // Define inputdata at the pipeline level
    }

    stages {
        stage('Call Management API') { // A single stage that encompasses both steps
            steps {
                script {
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
                        url: 'https://localhost:9164/management/applications?carbonAppName=SuccessSampleGuarantyDelivaryCompositeExporter',
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

                    if (SecondstatusCode == 200) {
                        // Handle the case when SecondstatusCode is 200
                        def jsonResponseSecond = new groovy.json.JsonSlurper().parseText(SecondresponseBody)
                        echo "Parsed JSON Response Second: ${jsonResponseSecond}"
                    } else if (SecondstatusCode == 404) {
                        // Handle the case when SecondstatusCode is 404
                        echo "Second endpoint returned a 404 status code, attempting third endpoint"

                        // Step 3: Call the Third Endpoint to get the number of Carfiles Deployed
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

                        // Capture the response status code and content
                        def ThirdstatusCode = resthree.getStatus()
                        def ThirdresponseBody = resthree.getContent()

                        echo "Response Status Code: ${ThirdstatusCode}"
                        echo "Response Body: ${ThirdresponseBody}"

                        if (ThirdstatusCode == 200) {
                            def jsonResponseThird = new groovy.json.JsonSlurper().parseText(ThirdresponseBody)
                            echo "Parsed JSON Response Third: ${jsonResponseThird}"
                        } else {
                            error("Third endpoint request failed with status code ${ThirdstatusCode}")
                        }
                    } else {
                        error("Second endpoint request failed with status code ${SecondstatusCode}")
                    }
                }
            }
        }
    }
}
