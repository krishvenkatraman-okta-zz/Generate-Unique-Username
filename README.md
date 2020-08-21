**Unique-username-email-generation**

### <span style="text-decoration:underline;">Overview</span>

To Onboard users in an organization, IT needs to generate unique username and Email addresses for the end users. 

This flow currently provides two things.
1) Generate unique username: This username is uniquely generated validating against Okta. You can validate this against any public API. At this time of writing Okta does not have a Onprem connector for Workflow. But you can pull the identities in Okta and workflow can use that for uniqueness check.

2) Generate unique Email : In this example there is three ways unique email address is generated.
    a) Validate against O365 Proxy Addresses attribute for Organizations using O365
    b) Validate against Google email aliases for Organizations using google mail
    c) Validate the Proxy Address against Okta directly, if Okta is the master for provisioning users in the target mailSystem.
    
    
    In the email example i use a BrandID which is used to determine the mail Domain example: Jeykrish.com,kriyahub.com etc. and target email system, Whether O365 or Okta or Google. If you just have a single email domain, You don't need to use this table and you can remove the brand logic inside the workflow.

**Workflow diagram.**

![image](https://user-images.githubusercontent.com/14205843/90941011-adabc780-e3c5-11ea-9972-a47cf1ff1e46.png)

    
    
### <span style="text-decoration:underline;">Before you get Started / Prerequisites</span>
Before you get started, you will need:

Access to an Okta tenant with Okta Workflows enabled for your org
Access to O365 or Google tenant. 

You can trigger the flow in two ways.
   1) User create Event . Which is more asyncronous way.
   2) Import inline hook. Where you can call this workflow using Okta import inline hook. Enabling import inline hook is documented here. Also shown in the video.
           https://developer.okta.com/docs/reference/import-hook/#enabling-a-user-import-inline-hook
           

### <span style="text-decoration:underline;">Design consideration</span>

Should not use inlinehook if your flow is big. When Okta calls your external service, it enforces a default timeout of 3 seconds. Okta will attempt at most one retry. So if your flow is big and going to run for a longer time, you can use the Okta user create event to generate unique username and email.


### <span style="text-decoration:underline;">Setup Steps</span>

1.Import the username and email workflow flopack in your workflow environment.
2.Create a table. Below is a sample data for the table. You can use your own domains for testing.

![image](https://user-images.githubusercontent.com/14205843/90940864-24949080-e3c5-11ea-875e-5ba3f8415238.png)

brandID represents attribute coming from HR system, emailTenent represents the target to check for email uniqueness. brandDomain represents the maildomain. Both emailTenant and brandDomain are derived from brandId. You can ignore this if you only one single domain.

3.Establish the connections to the target apps inside workflow, O365,GoogleApps and Okta.
4. Update the connection in the flows.
5. Extend the Okta profile with proxyAddresses attribute(String Array).If you are using Okta for validating the proxyAddress
6. Integrate CSV directory with Okta. If you have another HR system, You can also hook that to test end to end workflow.
https://help.okta.com/en/prod/Content/Topics/Directory/csv-integration-get-started.htm
7) Get the hookd endpoints from 001-PRD-inlineHookHandler-V1 flow which is setup inside the Uniqueusername folder. Click on the first card endpoint settings at the bottom and copy the invoke URL to the import inline hook endpoint settings in Okta.

### <span style="text-decoration:underline;">Testing this Flow</span>

1) If you have setup the CSV directory. Below is sample entry you can create.

**Below is the sample CSV data.**

empID,firstName,lastName,userName,email,emp_status,brandId,managerId
121,Tony,Stark,Tony.stark,tony.stark@gamer.com,active,30104,kvr@jeykrish.com

2) Configure CSV directory in Okta with all the attributes.
3) Associate the inline hook with CSV directory.
4) Import the file. This should trigger a inline hook call to the workflows endpoint.
5) Workflow should return the unique username and email address for the user.


### <span style="text-decoration:underline;">Limitations & Known Issues</span>
1) Okta workflows does not have any on-premise connector at this time for writing. So all the target should be accessible in public and shall be exposed as an API endpoint.
2) When Okta calls your external service, it enforces a default timeout of 3 seconds. Okta will attempt at most one retry. A request is not retried if the customer endpoint returns a 4xx HTTP error code
