---
title: Automating Employment Contracts with Sharepoint List, SignatureAPI and Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to streamline the employment contract process by automatically sending, signing, and storing contracts. By integrating  **Sharepoint List** (for storing the onboarding pack with the employee details), **SignatureAPI connector** (for electronic signatures), and the **Power Automate** (for approval-and-signature workflow), you can eliminate manual errors and delays in onboarding new employees.

### What You’ll Learn

* How to trigger a flow with a new Sharepoint List item.
* Retrieving and pre-filling a DOCX contract template from Sharepoint List.
* Creating and sending a signature envelope using SignatureAPI.
* Monitoring the signing process and retrieving the signed document.
* Saving the signed contract and notifying HR automatically.

### The Problem

HR departments often struggle with manually handling contracts, causing delays in onboarding. Common issues include:

* **Slow processing** – manual tasks create delays.
* **Errors** – mistakes from manual data entry.
* **Tracking difficulty** – challenges in monitoring signing status.

### How Automation Helps

Automation simplifies this process by:

* Automatically sending contracts to the employee.
* Using templates pre-filled with employee details.
* Tracking signature status and storing documents automatically.
* Informing HR instantly once contracts are signed.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Sharepoint List** – For storing the onboarding pack with the employee details.
* **Outlook** – For sending notifications (other email providers also work).

## Flow Overview

The automation process follows these steps:

1. **Trigger:** New Sharepoint List item creation starts the flow.
2. **Data Retrieval:** Get onboarding pack with employee details and fetch the contract template from Sharepoint List.
3. **Signature Process:** Create an envelope via SignatureAPI, add recipient details, and attach the DOCX.
4. **Monitoring:** Wait for the contract to be signed.
5. **Storage & Notification:** Save the signed document in Sharepoint and notify HR via email.


This process automates envelope creation and sending when a new item with an attachment is added to the SharePoint List.

Here’s what your final Power Automate flow will look like:

> Screenshot of the completed flow

## Step-by-Step Tutorial

Follow these steps to automate your employment contract process using Sharepoint List, SignatureAPI, and Microsoft Power Automate:

### Step 1: Prepare the Contract Template

First, create or update your employment contract template by adding placeholders for dynamic fields (employee details) and defining where the employee will sign.

**To prepare your template:**

1. Open your existing employment contract document (DOCX format) in Microsoft Word.
2. Identify each place where employee details should be dynamically inserted (e.g., first name, last name, email).
3. Insert placeholders using **double curly brackets** around descriptive keys. Examples:
- Employee first name: `{{employee.first_name}}`
- Employee last name: `{{employee.last_name}}`
- Employee email: `{{employee.email}}`
4. Define the location for the signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[employee_signature]]`

**Example placeholder usage in your document:**

> *Dear `{{employee.first_name}}` `{{employee.last_name}}`,*  
> *Please review and sign your employment contract below:*  
> `[[employee_signature]]`

5. Save your template.

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

![Word Template Example](/images/word/employee-template.png)

### Step 2: Create the Sharepoint List

First, create a Sharepoint List to store the employee details.

1. Visit [Microsoft Lists](https://www.microsoft.com/en-us/microsoft-365/microsoft-lists) and sign in.
2. Click on **"New List"** and then click on **"Blank List"**.

    ![New List](/images/sharepoint-lists/new-list.png)
3. Rename the list, and save it to the Sharepoint Site.
    
    ![Rename List](/images/sharepoint-lists/rename-list.png)
4. Hide the Title column and add the following ones:
  - **First Name** (Text)
  - **Last Name** (Text)
  - **Email Address** (Text)
    
    ![Add Columns](/images/sharepoint-lists/new-column.png)
    ![Add Columns](/images/sharepoint-lists/new-column-2.png)

5. Save and publish the list.

### Step 3: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered whenever a new item with an attachment is added to the Sharepoint List.

#### 3.1 Configure the Trigger

First, set the flow trigger to run whenever a new item with is added to the Sharepoint List.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Add Trigger. Name your flow, select **"When an item is created"** from the Sharepoint connector, and then click **Create**.

    ![Trigger](/images/powerautomate/sharepoint-list-flow/trigger.png)
3. Select the Site Address and the list you created earlier.

    ![Select List](/images/powerautomate/sharepoint-list-flow/select-list.png)

#### 3.2 Retrieve Employee Details from Sharepoint List

Next, retrieve the employee details from the Sharepoint List.

1. Add the action **"Get attachments"** from the Sharepoint connector.
2. Select the Site Address, List Name and Item ID from the previous step with Dynamic Content.

    ![Get Attachments](/images/powerautomate/sharepoint-list-flow/get-attachments.png)

#### 3.3 Get file content

1. Add the **"Get file content"** action from the Sharepoint connector.
2. Select the Site Address.
3. Add to the File Identifier the ID from the **Get attachments** step with Dynamic Content.
    
    ![Get file content](/images/powerautomate/sharepoint-list-flow/get-file.png)

Note: Power Automate will automatically add this to an Apply Each loop. This just means it will loop through all attachments you make to the item. This won’t affect the behavior, as you’re only attaching one document (If more attachments ara added to any item, its important that it has the same fields as the template).



### Step 4: Set Up the Signature Process

In this step, you'll configure SignatureAPI to create, send, and track the signature process.

#### 4.1 Create a SignatureAPI Envelope

Begin by creating an envelope to hold your contract and signature process.

1. Add the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).
3. Set an **Envelope Title** (e.g., employee first name) and a message using dynamic content.

    ![Create envelope](/images/powerautomate/sharepoint-list-flow/create-envelope.png)

#### 4.2 Add the Recipient

Next, specify who will receive and sign the contract.

1. Outside the **"For each loop"**, add **"Add Recipient"** action from the SignatureAPI connector.
2. Map **Recipient Name** and **Recipient Email** using form details (Dynamic Content).
3. Set the **Recipient Key** (e.g., "employee"), matching your DOCX placeholders.

    ![Add recipient](/images/powerautomate/sharepoint-list-flow/add-recipient.png)

#### 4.4 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with employee details.

1. Add **"Add a Template – DOCX"** action.
2. Select **File Content** from the **Get file content** action.
3. Select the Envelope ID from the **Create an Envelope** action.
4. Set the **Document Title** (e.g., "Employment Contract").
5. Ensure your DOCX template uses placeholders (`{{employee.first_name}}`, etc.) and map each field to the corresponding dynamic content from your form.

    ![Add template](/images/powerautomate/sharepoint-list-flow/add-template.png)

#### 4.5 Define Signature Placement

Specify where the employee should sign on the document.

1. Add **"Add a Place – Signature"** action.
2. Set the **Document ID** from the dynamic content.
3. Use the placeholder (e.g., `[[employee_signature]]`) from your DOCX template.
4. Set the **Recipient Key** from the dynamic content.

    ![Add signature](/images/powerautomate/sharepoint-list-flow/add-signature.png)


#### 4.6 Start the Signing Process

Trigger the sending of your envelope to the employee for signing.

1. Add **"Start Envelope"** action from the SignatureAPI connector.
2. Select the appropriate **Envelope ID** from dynamic content.

    ![Start envelope](/images/powerautomate/sharepoint-list-flow/start-envelope.png)

### Step 5: Monitor and Finalize the Contract

Next, configure your flow to wait for the signing to complete, retrieve the signed contract, and notify HR.

#### 5.1 Wait for Signature Completion

Pause the flow until the employee signs the contract.

1. Add **"Wait for Envelope Completion"** action from the SignatureAPI connector.
2. Select the correct **Envelope ID**.

    ![Wait for envelope](/images/powerautomate/sharepoint-list-flow/wait-envelope.png)

#### 5.2 Retrieve the Signed Contract

Once signed, automatically retrieve the completed document.

1. Add **"Get a Deliverable"** action from the SignatureAPI connector.
2. Select the correct **Deliverable ID** from dynamic content.

    ![Get deliverable](/images/powerautomate/sharepoint-list-flow/get-deliverable.png)

#### 5.3 Save the Signed Contract to Sharepoint

Save the signed document for record-keeping.

1. Add **"Create File"** action (Sharepoint connector).
2. Select the **Site Address** and **Folder Path**.
3. Set the **File Name** (ending in `.pdf`).
4. Map **File Content** from the deliverable.

    ![Save file](/images/powerautomate/sharepoint-list-flow/save-file.png)

#### 5.4 Notify HR via Email

Automatically inform HR that the contract has been signed and saved.

1. Add **"Send an Email"** action (Outlook connector).
2. Configure the email recipient (HR), subject, and message.
3. Attach the signed contract file from dynamic content.

    ![Send email](/images/powerautomate/sharepoint-list-flow/send-email.png)

### Step 6: Test Your Automation

Finally, test the entire process end-to-end.

1. Save your Power Automate flow.
2. Create a new item in the Sharepoint List. Ensure to attach the DOCX template to the item.
3. Verify:
  - Contract is sent to the employee.
  - Signature process initiates correctly.
  - Signed contract saves successfully in Sharepoint.
  - HR receives an email notification with the signed contract attached.

*Use the following checklist:*

- [ ] Contract sent successfully.
- [ ] Employee receives and signs contract.
- [ ] Signed document stored correctly in Sharepoint.
- [ ] HR receives email notification with attachment.

## Troubleshooting & FAQ

### Common Issues:

- **API Key Errors:** Ensure your SignatureAPI key is correct and authenticated.
- **Dynamic Content Mapping:** Double-check placeholder naming in your DOCX file matches exactly with dynamic content mappings.
- **File Access Issues:** Verify permissions and file paths in Sharepoint.

### Frequently Asked Questions:

- *What if the contract isn't sent?*  
  Check your SignatureAPI dashboard for errors, and verify recipient details.

- *Can I adapt this for other document types?*  
  Yes, this method is adaptable for any automated document-signing workflow.

## Best Practices & Security

- Always securely manage API keys.
- Regularly check flow runs in Power Automate for any errors.
- Document any flow or template changes for future reference.

## Additional Resources

- [SignatureAPI Documentation](https://signatureapi.com/docs)
- [Microsoft Sharepoint List Documentation](https://www.microsoft.com/en-us/microsoft-365/microsoft-lists)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of sending, signing, and managing employment contracts. This efficient workflow frees your HR team from repetitive tasks and ensures new employees have a smooth onboarding experience.

**Happy automating!**