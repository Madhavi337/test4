pipeline {
    agent any
    stages {
        stage('Call Endpoint') {
            steps {
                script {

                    
                    
                    def response = httpRequest(
                        url: 'https://localhost:9164/management/login',
                        httpMode: 'GET', // Use GET, POST, or other HTTP methods as needed
                        customHeaders:[[name:"Authorization",value:"Basic YWRtaW46YWRtaW4="]],
                        acceptType: 'APPLICATION_JSON',
                        responseHandle: 'NONE', // Use 'NONE' to capture the raw response
                        timeout: 60, // Set the timeout in seconds
                        validResponseCodes: '200', // Define the expected response code(s)
                        ignoreSslErrors: true,// Set to true if the endpoint uses self-signed SSL certificates
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
                        echo "API call was successful. ResponseBody: ${responseBody}"
                       

        // Your JSON response
                    
                    // Parse the JSON to extract the access token
                    def accessToken = readJSON text: jsonResponse

                    // Access the value of AccessToken
                    def accessTokenValue = accessToken.AccessToken

                    echo "Access Token: ${accessTokenValue}"

                    // Now, you can use 'accessTokenValue' in subsequent steps

                    } else {
                        error "API call failed with status.Response Status Code: ${statusCode}."
                    }
                    
                
                }
            }
        }
    }

}