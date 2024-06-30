# BlueCorp 3PL Sales Order Automation

By using Azure Integration service, to automate the sales orders processes between Blue Corp's logistic team and 3PL. 

## Design Ojectives 

* All integrations must use Azure Integration services;
* All integrations must be built and deployed automatically with Azure DevOps Pipelines;
* All integrations must recover gracefully on transient errors (e.g. SFTP site temporarily unavailable) without the need to resubmit from D365;
* All integrations telemetry & logging must be published to Azure for monitoring and alerting purposes;
* All systems must use secure and modern authentication mechanism.
* All data at-rest and in-transit must be encrypted;


## High level Design 
* D365 Finance & Operations: Triggers an HTTP POST request with a JSON payload when the "ready for dispatch" button is clicked.
* Azure Logic Apps - workflow triggered by "Receives the HTTP POST request" with Oauth 2.0 authentication from Blue Corp MS Entra organziation.
* To validate the request body with JSON schema
* To compare the payload control number with the last used control number from SQL/Dataverse, if lower then last used number, then bypass the duplciated payload.
* To convert "Container Type" with matched type in 3PL definition
* To use two "For Each " loops to access sale order item level and build a new CSV row for each order item.
* To append  CSV row to an array and convert it to a CSV table.
* To connect to 3PL SFTP site and create a CSV file on target folder, ensure retry policy applied to tolerance SFTP site connection issues.
* If successful created/upload CSV file, then update the last used control number in SQL/Dataverse.
* If any error, to catch and send payload control number and the error deatils to system admin. 
  

### Dependencies

* Setup virtual network integration and Vnet Integration for Outbound traffic, so one public IP can be NAT and confirmed for 3PL SFTP whitelisting.
* Prepare Azure AD OAuth 2.0 client authorization
  * Register an application in Azure portal under Azure AD - App registrations and get the client secret for access token generation.
  * Create new authorization policy and managed identity to support API management service, also remove the SAS key from the operation policy and only leave the api-version value.
* To store the last used control number, Need SQL or Dataverse account and the data table.
  

### DevOps Pipielines CI/CD 
* How/where to download your program
* Any modifications needed to be made to files/folders

### Executing program

* How to run the program
* Step-by-step bullets
```
code blocks for commands
```

## Help

Any advise for common problems or issues.
```
command to run if program contains helper info
```

## Authors

Contributors names and contact info

ex. Dominique Pizzie  
ex. [@DomPizzie](https://twitter.com/dompizzie)

## Version History

* 0.2
    * Various bug fixes and optimizations
    * See [commit change]() or See [release history]()
* 0.1
    * Initial Release

## License

This project is licensed under the [NAME HERE] License - see the LICENSE.md file for details

## Acknowledgments

Inspiration, code snippets, etc.
* [awesome-readme](https://github.com/matiassingers/awesome-readme)
* [PurpleBooth](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)
* [dbader](https://github.com/dbader/readme-template)
* [zenorocha](https://gist.github.com/zenorocha/4526327)
* [fvcproductions](https://gist.github.com/fvcproductions/1bfc2d4aecb01a834b46)
