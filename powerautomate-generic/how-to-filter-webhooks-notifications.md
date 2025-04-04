---
title: Filter SignatureAPI Webhooks notifications using Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to filter SignatureAPI webhooks notifications using Microsoft Power Automate.

### What You’ll Learn

* How to trigger a flow with a signatureAPI trigger.
* How to filter SignatureAPI webhooks notifications with the Microsoft Power Automate connector. 
* How to notify the specific department via email with Microsoft Outlook connector.

### The Problem

Many companies need to filter webhooks notifications to ensure only relevant departments receive emails for specific events (It can also be sent via Slack or other channels). For example, filtering can be based on the department responsible for handle the clients financial documents.



### How Automation Helps

Automation helps to filter the notifications for the specific department by:

* Automatically filtering the notifications for the specific department.


## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Microsoft Outlook account** – To send the email notifications.


Also, this tutorial assumes that you have other Power Automate flow that creates (and starts) envelopes with SignatureAPI. That flow must have the **"Create an envelope"** action from SignatureAPI connector including the topics in the body of the envelope that is defined in the **"Envelope Topics"** from the Advanced parameters. In this case the topic is "sales-department".

![Envelope topics](/images/powerautomate/filter-webhooks-flow/envelope-topics.png)


## Flow Overview

The automation process follows these steps:

1. **Trigger:** SignatureAPI trigger starts the flow. In this case, the trigger is when the deliverable is generated. 
2. **Filter:** Filter the notifications for the specific department.
3. **Notify:** Send a notification to the specific department.

Here’s what your final Power Automate flow will look like:

![Flow](/images/powerautomate/filter-webhooks-flow/complete-flow.png)

## Step-by-Step Tutorial

Follow these steps to automate the filtering of webhooks notifications with SignatureAPI and Microsoft Power Automate and notify the specific department via email.

### Step 1: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered by the SignatureAPI trigger.

#### 1.1 Configure the Trigger

First, set the flow trigger, in this case with the SignatureAPI trigger for the deliverable generation.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Name the flow and choose the trigger **"When a deliverable is generated"** (from the SignatureAPI connector).

    ![Trigger](/images/powerautomate/filter-webhooks-flow/trigger.png)

#### 1.2 Filter the notifications for the specific department

Filter the notifications for the envelopes that has the same topic defined in its body.

1. In the Trigger, define in the **"Topics"** field the specific topic that will filter the notifications. In this case, enter "sales-department". This will ensure the trigger only fires for envelopes that have this same topic defined in their body (as explained in the requirements section).

2. The topic filtering acts as a label system - only envelopes tagged with matching topics will trigger notifications, allowing you to route different types of documents to different departments.

    ![Filter](/images/powerautomate/filter-webhooks-flow/add-topics.png)

#### 1.3 Retrieve the Signed Contract

Retrieve the completed document.

1. Add **"Get a Deliverable"** action.
2. Select the correct **Deliverable ID** using dynamic content.

    ![Get deliverable](/images/powerautomate/filter-webhooks-flow/get-deliverable.png)

#### 1.4 Send the notification to the specific department

Next, send the notification to the specific department with the Outlook connector.

1. Add the action **"Send an email"** from the Microsoft Outlook connector.
2. In the **"To"** field, add the email address of the department that will receive the notification.
Attach the signed contract file from dynamic content. Ensure to use the **"File Content"** from the **"Get a Deliverable"** action, and filename (ending in `.pdf`).

   ![Send email](/images/powerautomate/filter-webhooks-flow/send-email.png)

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
- **Outlook Account:** Ensure the Outlook account is correct and accessible.

### Frequently Asked Questions:

- **What if the notification isn't received?**  
  Check your Outlook account for the notification.

## Best Practices & Security

- Always securely manage API keys.
- Regularly check flow runs in Power Automate for any errors.
- Document any flow or template changes for future reference.

## Additional Resources

- [SignatureAPI Documentation](https://signatureapi.com/docs)
- [Microsoft Outlook Documentation](https://support.microsoft.com/outlook)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of filtering webhooks notifications with SignatureAPI and Microsoft Power Automate and notify the specific department via email. This efficient workflow frees your team from manual follow-ups and filters the notifications for the specific department.

**Happy Automating!**