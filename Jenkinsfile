pipeline {
    agent any

    environment {
        inputdata = '' // Define inputdata at the pipeline level
    }

    stages {
        stage('Call First and Second Endpoints') { // A single stage that encompasses both steps
            steps {
                script {
                    // Step 1: Call the First Endpoint
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

                    // Step 2: Call the Second Endpoint
                    echo "AccessTokenFirst: ${inputdata}"
                    def res = httpRequest(
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
                    def SecondstatusCode = res.getStatus()
                    def SecondresponseBody = res.getContent()

                    echo "Response Status Code: ${SecondstatusCode}"
                    echo "Response Body: ${SecondresponseBody}"

                    if (SecondstatusCode == 200) {
                        def jsonResponseSecond = new groovy.json.JsonSlurper().parseText(SecondresponseBody)
                        echo "Parsed JSON Response Second: ${jsonResponseSecond}"
                    } else {
                        error("Second endpoint request failed with status code ${SecondstatusCode}")
                    }
                }
            }
        }
    }
}
