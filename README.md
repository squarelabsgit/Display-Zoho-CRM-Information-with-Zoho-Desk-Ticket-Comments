# Display-Zoho-CRM-Information-with-Zoho-Desk-Ticket-Comments
Blog Post: https://www.squarelabs.com.au/post/how-to-display-zoho-crm-information-with-zoho-desk-ticket-comments

YouTube: https://youtu.be/0B5-iSRWBIM

Efficiency is the key to success in customer support, and finding ways to streamline workflows can make a significant impact. In this article, we'll explore a custom solution to enhance the  Zoho Desk and Zoho CRM integration using workflows and the Zoho Desk API. Specifically, we'll focus on leveraging this integration to display Zoho CRM information with Zoho Desk ticket comments, providing agents with instant access to critical data.

![Example of Desk Ticket Comment](https://static.wixstatic.com/media/c8c3af_8bb37be65d87403683aa7b18f294bed8~mv2.png)

By utilising custom workflows and the Zoho Desk API, you can create a process that automatically pulls and displays pertinent Account, Contact, or Custom Module data from Zoho CRM directly into ticket as a comment. This ensures that Desk agents have immediate access to the information they need without navigating between different systems or performing manual searches.

##Zoho CRM > Zoho Desk Native Integration
We need to set this up as it will create the link between the records by storing the Zoho CRM Contact and Account record ID on the Zoho Desk Contact and Account.

Head to Settings > Settings > Marketplace > Zoho

1. Click Zoho CRM
2. Click Integrate
3. Click Authorise
4. Select your sync type
5. Map your Account and Contact fields
6. Click Start Sync
7. You can modify your sync preferences here further if required

## Connections
As apart of this automation we will be getting information from Zoho CRM and then displaying it via the Zoho Desk API. We will need 2 connections for this, of course you can put them into a single connection if you prefer, I just opt to keep my connections seperate for each application.

### Connection Names and Scopes

1. crm_connection
   * ZohoCRM.modules.ALL
2. desk_connection
   * Desk.tickets.UPDATE

Head to Settings > Developer Space > Connections

1. Click Create Connection
2. Select Zoho OAuth
3. Enter a Connection Name
4. Select the required scopes (listed above).
5. Click Create and Connect
6. Click Connect
7. Select your Production System
8. Click Accept

## Functions
We will be creating a functions for this automation and the code is provided below.

You will also need to retrieve your orgId which is located at the bottom of the page here: Settings > Developer Space > API

To create a function go to Settings > Developer Space > Functions

1. Click New Function
2. Enter a Function Name (No Spaces)
3. Set the Category to Automation
4. Set Module to Tickets
5. Enter code below
6. Update the tickets arguments and set ticketId to Ticket ID
7. Click Save

![Example of Function Arguments](https://static.wixstatic.com/media/c8c3af_b9c61ac256914f808f34cc3b7c1ee004~mv2.png)

Inside our code example we are returning the field 'Important Account Note' but you can modify the code to get other data from your system that you want to display.

```js
//Required Variables
orgId = <ENTER_ORG_ID_HERE>;
//Get Ticket Info
ticketMap = zoho.desk.getRecordById(orgId,"tickets",ticketId);
//info ticketMap;
//Get the CRM Contact Information - Uncomment if you want to use to get Contact Information
/*
contactId = ticketMap.get("contactId");
contactMap = zoho.desk.getRecordById(orgId,"contacts",contactId);
crmContactId = contactMap.get("zohoCRMContact").get("id");
crmContactMap = zoho.crm.getRecordById("Contacts",crmContactId,"crm_connection");
//info crmContactMap;
*/
//Get the CRM Account Information
accountId = ticketMap.get("accountId");
accountMap = zoho.desk.getRecordById(orgId,"accounts",accountId);
crmAccountId = accountMap.get("zohoCRMAccount").get("id");
crmAccountMap = zoho.crm.getRecordById("Accounts",crmAccountId,"crm_connection");
//info crmAccountMap;
//Get required account information to be displayed in the comment.
importantAccountNote = crmAccountMap.get("Important_Account_Note");
//If there is no note there is no reason to create the comment on the ticket.
if(!isnull(importantAccountNote))
{
	//Create the HTML Comment
	htmlComment = "";
	htmlComment = htmlComment + "<h1 style=\"color:red;\">IMPORTANT NOTE</h1>";
	htmlComment = htmlComment + "<p style=\"color:black;\">" + importantAccountNote + "</p>";
	//Prepare the API URL With the Ticket ID
	apiURL = "https://desk.zoho.com/api/v1/tickets/" + ticketId + "/comments";
	//Prepare the parameters
	paramMap = Map();
	paramMap.put("content",htmlComment);
	paramMap.put("isPublic",false);
	paramMap.put("contentType","html");
	//Send the Comment Via API
	addComment = invokeurl
	[
		url :apiURL
		type :POST
		parameters:paramMap.toString()
		connection:"desk_connection"
	];
	info addComment;
}
```

## Workflow
We are now ready to associate the new function to a workflow.

Head to Settings > Automation > Workflows

1. Click Create Rule
2. Enter a Rule Name
3. Click Next
4. Check the Create checkbox for Execute On
5. Click Next
6. Enter conditions if you need.
7. Click Next
8. Under Actions click the '+' Button
9. Scroll Down to Custom Functions and select Existing
10. Select the Function you created before
11. Click Save
12. That completes this small but powerful customisation, hopefully that helps boost your agent productivity, streamline operations, and deliver exceptional customer experiences.

Need Help? [Contact us!](https://www.squarelabs.com.au/contact-us)


## Resources

GitHub Code: https://github.com/squarelabsgit/Display-Zoho-CRM-Information-with-Zoho-Desk-Ticket-Comments 

Zoho API Documentation: https://desk.zoho.com/DeskAPIDocument#TicketsComments 

<a href="http://www.youtube.com/watch?feature=player_embedded&v=0B5-iSRWBIM" target="_blank"><img src="http://img.youtube.com/vi/0B5-iSRWBIM/0.jpg" 
alt="YouTube Video" width="240" height="180" border="10" /></a>
