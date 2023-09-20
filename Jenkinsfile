pipeline {
    agent any
    stages {
        stage('Call Endpoint') {
            steps {
                script {
                    def response = httpRequest(
                        url: 'http://localhost:3000/management',
                        httpMode: 'GET', // Use GET, POST, or other HTTP methods as needed
                        contentType: 'APPLICATION_JSON',
                        responseHandle: 'NONE', // Use 'NONE' to capture the raw response
                        timeout: 60, // Set the timeout in seconds
                        validResponseCodes: '200', // Define the expected response code(s)
                        ignoreSslErrors: true // Set to true if the endpoint uses self-signed SSL certificates
                    )

                    // Capture the response status code and content
                    def statusCode = response.getStatus()
                    def responseBody = response.getContent()

                    echo "Response Status Code: ${statusCode}"
                    echo "Response Body: ${responseBody}"

                    // You can now process or parse the response as needed
                    // For example, parsing JSON:
                    def jsonResponse = new groovy.json.JsonSlurper().parseText(responseBody)
                    echo "Parsed JSON Response: ${jsonResponse}"

                 // Check the HTTP response status
                    if (response.status == 200) {
                        echo "API call was successful. Response body: ${responseBody}"
                    } else {
                        error "API call failed with status ${statusCode}."
                    }

                }
            }

        }

    }
}