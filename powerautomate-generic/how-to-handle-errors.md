---
title: Handle SignatureAPI Errors with Slack Notifications in Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to handle most common errors with SignatureAPI and Microsoft Power Automate. You can use this tutorial for sending Slack notifications for other triggers, you can find more information in the [SignatureAPI documentation](https://signatureapi.com/docs).

### What You’ll Learn

* How to trigger a flow with a signatureAPI trigger.
* How to handle notify the event to Slack with the Microsoft Power Automate connector.

### The Problem

In HR departments, it's common to send important documents like employment contracts, policy acknowledgements, or tax forms for electronic signature. However, sometimes:

* The email to sign for the employee bounces.

When these issues happen, HR teams often don’t notice immediately, which leads to delays in onboarding or compliance processes.

For example, when a new hire doesn't sign their contract on time because their email bounced, the onboarding process stalls, leaving both the HR manager and the employee unaware until it's too late.

### How Automation Helps

Automation helps HR teams stay on top of signature issues by:

* Monitoring contract delivery and signature status in real time.
* Automatically detecting when a contract bounced.
* Instantly notifying the HR team on Slack when something goes wrong.
* Reducing manual follow-ups and delays in the onboarding or document signing process.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Slack Account** – For notifications.

## Flow Overview

The automation process follows these steps:

1. **Trigger:** SignatureAPI trigger starts the flow. In this case, the trigger is when the email to sign for the recipient bounces. But  you can do the same for other triggers, you can find more information in the [SignatureAPI documentation](https://signatureapi.com/docs).
2. **Notify Slack:** Send a notification to Slack with the error details.

Here’s what your final Power Automate flow will look like:

![Flow](/images/powerautomate/handle-errors-flow/complete-flow.png)

## Step-by-Step Tutorial

Follow these steps to automate the process of handling errors with SignatureAPI and Microsoft Power Automate.

### Step 1: Create the Slack Channel

First, create a Slack channel to receive the notifications.


1. Go to **Slack** and create a new channel.
2. Click **"Create Channel"**.

   ![Slack Channel](/images/slack/create-channel.png)

3. Name (e.g. "bounced-emails") the channel and click **"Create"** (make it public or private depending on your needs).

   ![Slack Channel](/images/slack/name-channel.png)


### Step 2: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered by the SignatureAPI trigger.

#### 2.1 Configure the Trigger

First, set the flow trigger, in this case with the SignatureAPI trigger for the recipient bounce.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Choose the trigger **"When a recipient bounces"** (SignatureAPI).

   ![Trigger](/images/powerautomate/handle-errors-flow/trigger.png)


#### 2.2 Send the notification to Slack

Next, send the notification to Slack with the Microsoft Power Automate connector.

1. Add the action **"Post message"** from the Slack connector. You will need to authorize the Slack connector with your Slack account.

    ![Slack connector](/images/powerautomate/handle-errors-flow/post-message-slack.png)

    ![Slack connector](/images/powerautomate/handle-errors-flow/authorize-slack.png)


2. Select the **Channel Name**.

3. Set the **Message** using the Dynamic Content from the SignatureAPI trigger.

    ![Slack connector](/images/powerautomate/handle-errors-flow/set-message.png)



### Step 3: Test Your Automation

Finally, test the entire process end-to-end.

1. Save your Power Automate flow.
2. Create a new envelope with a recipient that will bounce.
3. Verify:
  - Slack notification is received.

*Use the following checklist:*

- [ ] Slack notification is received.

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
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of handling errors with SignatureAPI and Microsoft Power Automate. This efficient workflow frees your HR team from manual follow-ups and delays in the onboarding or document signing process.

**Happy automating!**