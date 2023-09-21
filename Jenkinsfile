pipeline {
    agent any
    stages {
        stage('Call Endpoint') {
            steps {
                script {

                    
                    
                    def response = httpRequest(
                        url: 'https://localhost:9164/management/applications',
                        httpMode: 'GET', // Use GET, POST, or other HTTP methods as needed
                        customHeaders:[[name:"Authorization",value:"eyJraWQiOiJhM2UyOWM5ZS0xMjA0LTQzMjEtOTYzYi03ZThjOTViNWU0NjYiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczpcL1wvMDowOjA6MDowOjA6MDoxOjkxNjRcLyIsInN1YiI6ImFkbWluIiwiZXhwIjoxNjk1MzA1NzYxLCJzY29wZSI6ImFkbWluIn0.GL-wLhq6Y80xfsUF7Ha2St8S0PtOg__DX4T7sb1v4_muwvOV7sENsjWS1qPttFS00d5FQkpw6-V49tCT7udSf5CsZDuYnm7rIdG5pIw__tVOcxn39XB3G1aqfmF3UtPT7TtFVPGCYNmlTLmf_waIsJa-XcW6jYz6reLYxYHvWIC_60sXmWlDwLGRLQEaRpGsbnxtVZvOIdLAm8BpshootGlDgDUGBxy5L1H2mJAG33OBSL37mjdEFaoRvimmfbRRUhari0IWxrzEb5tvvTWVu2Vlzbx0ojxXwx0Bv-m0QA2zRGmrMYnlpnyNqm3MYDf4OwWVW64924kUZqQlHXhQ7w"]],
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
                    
    
                    
                
                }
            }
        }
    }

}