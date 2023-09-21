pipeline {
    agent any
    stages {
        stage('Call Endpoint') {
            steps {
                script {

                    
                    
                    def response = httpRequest(
                        url: 'https://localhost:9164/management/applications',
                        httpMode: 'GET', // Use GET, POST, or other HTTP methods as needed
                        customHeaders:[[name:"Authorization",value:"eyJraWQiOiJhZWNhODY0NS0yYmQwLTQ2ZWMtYTQ0ZC05MWNhYWVjYTcyOGQiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJodHRwczpcL1wvMDowOjA6MDowOjA6MDoxOjkxNjRcLyIsInN1YiI6ImFkbWluIiwiZXhwIjoxNjk1MzA1OTQyLCJzY29wZSI6ImFkbWluIn0.feHPi82MdeDGrjsXLgQDnULF0ozXV-GoWKdBeprbLUjGvqM_vfZVawmTGe04-H7-4s4274mxLo773SZ4TWpWhSgOE9TmsmK_7syQfLEQMu0jppVTAjcoj2sS7lbKteMWR47-IKW-eqr8QiNWwyVd2oLZCDTVa7Nsh8EWIafvKPWoPDNqzbsbAmk3xI3GwMd1RcWXrA2yGsaTeclj_c5vUewrCXmxGghUup5NgWQ1ie-BJIHyzloWlt9zwXze5uA6vGlsbJ5cCbY6Cj-Nh72Q_y8NX7aPDwfW1Q4Ctezl_kMo1UCvS0ygRrtMeDRMeoWqwLaL_AAkrgXL7DVl2kSebg"]],
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