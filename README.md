**Use Import Inline Hook to Create Unique Identifier **

### <span style="text-decoration:underline;">Overview</span>

To Onboard users in an organization, IT needs to generate unique username for the end users. Also Okta username needs to be unique.

This flow generates unique Okta username: This username is uniquely generated validating against Okta. You can validate this against any public API. At this time of writing Okta does not have a Onprem connector for Workflow. But you can pull the identities in Okta and workflow can use that for uniqueness check.

### <span style="text-decoration:underline;">Before you get Started / Prerequisites</span>
Before you get started, you will need:

1.Access to an Okta tenant with Okta Workflows enabled for your org

2.You can trigger the flow in two ways.
   a) User create Event . Which is more Asyncronous way. Often time you can do this by creating a user in Okta with EmployeeID or something unique as Okta Username for all users and Okta workflows can generate a unique username and update the OKta username attribute.
   b) Import inline hook. Workflow is called during import process.Enabling import inline hook is documented here. Also shown in the video.
           https://developer.okta.com/docs/reference/import-hook/#enabling-a-user-import-inline-hook
         
    **Also inline hook is useful to Link users, If there is a conflict of users**          

### <span style="text-decoration:underline;">Design consideration</span>

Should not use inlinehook if your flow is big. When Okta calls your external service, it enforces a default timeout of 3 seconds. Okta will attempt at most one retry. So if your flow is big and going to run for a longer time, you can use the Okta user create event to generate unique username.


### <span style="text-decoration:underline;">Setup Steps</span>

1.Import the username workflow flopack in your workflow environment.
3.Establish the connections to the Okta tenant.
4. Update the connection in the flows.
5. Integrate CSV directory with Okta. If you have another HR system, You can also hook that to test end to end workflow.
https://help.okta.com/en/prod/Content/Topics/Directory/csv-integration-get-started.htm
6) Get the hook endpoints from 001-PRD-inlineHookHandler-V1 flow which is setup inside the Uniqueusername folder. Click on the first card endpoint settings at the bottom and copy the invoke URL to the import inline hook endpoint settings in Okta.

![image](https://user-images.githubusercontent.com/14205843/90942964-b0f68180-e3cc-11ea-8331-96ddcb39ae50.png)


### <span style="text-decoration:underline;">Testing this Flow</span>

1) If you have setup the CSV directory. Below is sample entry you can create.

**Below is the sample CSV data.**

empID,firstName,lastName,userName,email,emp_status,brandId,managerId
121,Tony,Stark,Tony.stark,tony.stark@gamer.com,active,30104,kvr@jeykrish.com

2) Configure CSV directory in Okta with all the attributes.
3) Associate the inline hook with CSV directory.
4) Import the file. This should trigger a inline hook call to the workflows endpoint.
5) Workflow should return the unique username for the user.


### <span style="text-decoration:underline;">Limitations & Known Issues</span>
1) Okta workflows does not have any on-premise connector at this time for writing. So all the target should be accessible in public and shall be exposed as an API endpoint.
2) When Okta calls your external service, it enforces a default timeout of 3 seconds. Okta will attempt at most one retry. A request is not retried if the customer endpoint returns a 4xx HTTP error code
