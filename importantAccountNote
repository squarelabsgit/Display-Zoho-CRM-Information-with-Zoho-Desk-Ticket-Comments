//
//Refer to YouTube Video: https://youtu.be/0B5-iSRWBIM
//
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
