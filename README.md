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
  


### Task Design Time and Implementing details 
* Spent 45x mins to understand business case requirement and the key deliverables (OAuth 2.0, encryption, Outbound fixed IP and duplicated payload control number). 
* Spent about one hour to build draft flow with Power Automate (my current daily tool).
* Started Logic Apps with VS code and set up Azure/GitHub/DevOps accounts and enviroments. 
* Spent about two hours to "rebuild" workflow in VS code with Github source control.
* Spent about three hours to confirm Logic Apps OAuth 2.0, Telemetry and App insight, DevOPs Pipelines build and deploy scripts.


## Task Comments 
* Logic Apps provides more enterprise level workflow/integration service design capacity and reliable management platform.  
  * VS Code fully integrated with GitHub source code control.
  * To build DevOps piepiles with below sample link, however due to the Azure account and time issues, did't have a chance to complete ARM reosurce ID set up and full test.
  * https://github.com/Azure/logicapps/tree/master/azure-devops-sample
    
* Depends on business critical and the data volume, MS Power Automate provides below quick low code options.
  * Buildin OAuth 2.0 option for https request trigger with selectable users from same MS Entra tenant.
  * Automation center provides comprehensive visualizations that enable to monitor the health of the automations, quickly detect issues or trends, and troubleshoot problems.
  * Devloper's solution package and UAT test based Low code developement.
  

