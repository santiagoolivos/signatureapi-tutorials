---
title: Save and notify when signed documents are saved in Sharepoint with SignatureAPI and Microsoft Power Automate   
---

## Overview

This tutorial demonstrates how to save signed documents in Sharepoint with SignatureAPI and Microsoft Power Automate. This tutorial is for Sharepoint Online, but the same flow can be used for any other type of files administration, like OneDrive, Azure Blob Storage, etc.

### What You’ll Learn

* How to trigger a flow with a signatureAPI trigger.
* How to save signed documents in Sharepoint with the Microsoft Power Automate connector. 
* How to notify when signed documents are saved in Sharepoint with the Outlook connector.

### The Problem

In HR departments, it's common to send important documents like employment contracts, policy acknowledgements, or tax forms for electronic signature. However, sometimes:

* Difficult to know when the document is completed.
* The signed document is not saved in the correct Sharepoint folder.

When these issues happen, HR teams often don’t notice immediately, which leads to delays in onboarding or compliance processes.


### How Automation Helps

Automation helps HR teams stay on top of signature issues by:

* Monitoring contract delivery and signature status in real time.
* Automatically detecting when the document is completed.
* Automatically saving the signed document in the correct Sharepoint folder.
* Instantly notifying the HR team on Slack when the document is completed.
* Reducing manual follow-ups and delays in the onboarding or document signing process.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Sharepoint Online** – To save the signed document.
* **Outlook Account** – For notifications.

## Flow Overview

The automation process follows these steps:

1. **Trigger:** SignatureAPI trigger starts the flow. In this case, the trigger is when the deliverable is generated. 
2. **Save to Sharepoint:** Save the signed document in the correct Sharepoint folder.
3. **Notify HR:** Send a notification to the HR team with the document details.

Here’s what your final Power Automate flow will look like:

![Flow](/images/powerautomate/save-deliverables-sharepoint-flow/complete-flow.png)

## Step-by-Step Tutorial

Follow these steps to automate the process of saving signed documents in Sharepoint with SignatureAPI and Microsoft Power Automate.

### Step 1: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered by the SignatureAPI trigger.

#### 1.1 Configure the Trigger

First, set the flow trigger, in this case with the SignatureAPI trigger for the deliverable generation.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Name the flow and choose the trigger **"When a deliverable is generated"** (from the SignatureAPI connector).

    ![Trigger](/images/powerautomate/save-deliverables-sharepoint-flow/trigger.png)

#### 1.2 Retrieve the Signed Contract

Retrieve the completed document.

1. Add **"Get a Deliverable"** action.
2. Select the correct **Deliverable ID** from dynamic content.

    ![Get deliverable](/images/powerautomate/save-deliverables-sharepoint-flow/get-deliverable.png)

#### 1.3 Save the Signed Contract to Sharepoint

Save the signed document for record-keeping.

1. Add **"Create File"** action (Sharepoint connector).
2. Select the **Site Address** and **Folder Path**.
3. Set the **File Name** (ending in `.pdf`).
4. Map **File Content** from the deliverable.

    ![Save file](/images/powerautomate/save-deliverables-sharepoint-flow/save-file.png)


#### 1.4 Send the notification to the specific department

Next, send the notification to the specific department with the Outlook connector.

1. Add the action **"Send an email"** from the Microsoft Outlook connector.
2. In the **"To"** field, add the email address of the department that will receive the notification.
Attach the signed contract file from dynamic content. Ensure to use the **"File Content"** from the **"Get a Deliverable"** action, and filename (ending in `.pdf`).

   ![Send email](/images/powerautomate/save-deliverables-sharepoint-flow/send-email.png)

### Step 2: Test Your Automation

Finally, test the entire process end-to-end.

1. Save your Power Automate flow.
2. Create a new envelope with the topic "sales-department".
3. Sign the document.
4. Verify:
  - Email notification is received by the specific department.

*Use the following checklist:*

- [ ] Email notification is received by the specific department.

## Troubleshooting & FAQ

### Common Issues:

- **API Key Errors:** Ensure your SignatureAPI key is correct and authenticated.
- **Slack Channel:** Ensure the Slack channel is correct and accessible.

### Frequently Asked Questions:

- **What if the notification isn't received?**  
  Check your Slack channel for the notification.

## Best Practices & Security

- Always securely manage API keys.
- Regularly check flow runs in Power Automate for any errors.
- Document any flow or template changes for future reference.

## Additional Resources

- [SignatureAPI Documentation](https://signatureapi.com/docs)
- [Slack Documentation](https://slack.com)
- [Sharepoint Documentation](https://support.microsoft.com/sharepoint)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of notifying with Slack and saving signed documents in Sharepoint with SignatureAPI and Microsoft Power Automate. This efficient workflow frees your HR team from manual follow-ups and delays in the onboarding or document signing process.

**Happy automating!**