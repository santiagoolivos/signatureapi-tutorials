---
title: Notify and Save signed Documents using Slack, Sharepoint and SignatureAPI with Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to save signed documents in Sharepoint with SignatureAPI and Microsoft Power Automate.

### What You’ll Learn

* How to trigger a flow with a signatureAPI trigger.
* How to save signed documents in Sharepoint with the Microsoft Power Automate connector. 

This tutorial is for Sharepoint Online, but the same flow can be used for any other type of files administration, like OneDrive, Azure Blob Storage, etc.

### The Problem

In HR departments, it's common to send important documents like employment contracts, policy acknowledgements, or tax forms for electronic signature. However, sometimes:

<!-- hard to know when the document is completed and not being saved automatically in the required sharepoint folder  -->
* Difficult to know when the document is completed.
* The signed document is not saved in the correct Sharepoint folder.

When these issues happen, HR teams often don’t notice immediately, which leads to delays in onboarding or compliance processes.


### How Automation Helps

Automation helps HR teams stay on top of signature issues by:

* Monitoring contract delivery and signature status in real time.
* Automatically detecting when the document is completed.
* Instantly notifying the HR team on Slack when the document is completed.
* Reducing manual follow-ups and delays in the onboarding or document signing process.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Sharepoint Online** – To save the signed document.
* **Slack Account** – For notifications.

## Flow Overview

The automation process follows these steps:

1. **Trigger:** SignatureAPI trigger starts the flow. In this case, the trigger is when the deliverable is generated. 
2. **Notify Slack:** Send a notification to Slack with the document details.
3. **Save to Sharepoint:** Save the signed document in the correct Sharepoint folder.

Here’s what your final Power Automate flow will look like:

> Screenshot of the completed flow

## Step-by-Step Tutorial

Follow these steps to automate the process of handling errors with SignatureAPI and Microsoft Power Automate.

### Step 1: Create the Slack Channel

First, create a Slack channel to receive the notifications.


1. Go to **Slack** and create a new channel.
2. Click **"Create Channel"**.

   ![Slack Channel](/images/slack/create-channel.png)

3. Name the channel and click **"Create"** (make it public or private depending on your needs).

   ![Slack Channel](/images/slack/name-channel.png)


### Step 2: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered by the SignatureAPI trigger.

#### 2.1 Configure the Trigger

First, set the flow trigger, in this case with the SignatureAPI trigger for the deliverable generation.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Choose the trigger **"When a deliverable is generated"** (SignatureAPI).

#### 2.2 Retrieve the Signed Contract

Once signed, automatically retrieve the completed document.

1. Add **"Get a Deliverable"** action from the SignatureAPI connector.
2. Select the correct **Deliverable ID** from dynamic content.

> *Include annotated screenshot of deliverable retrieval.*


#### 2.3 Save the Signed Contract to Sharepoint

Save the signed document for record-keeping.

1. Add **"Create File"** action (Sharepoint connector).
2. Set the destination folder and filename (ending in `.pdf`).
3. Map **File Content** from the deliverable.

> *Include annotated screenshot of file saving.*

#### 2.4 Send the notification to Slack

Next, send the notification to Slack with the Microsoft Power Automate connector.

1. Add the action **"Post message"** from the Slack connector.
2. Select your Slack channel and the message from Dynamic Content.
3. Add the Dynamic Content from the SignatureAPI trigger.

### Step 3: Test Your Automation

Finally, test the entire process end-to-end.

1. Save your Power Automate flow.
2. Sign the document.
3. Verify:
  - Slack notification is received.
  - The signed document is saved in the correct Sharepoint folder.

*Use the following checklist:*

- [ ] Slack notification is received.
- [ ] The signed document is saved in the correct Sharepoint folder.

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