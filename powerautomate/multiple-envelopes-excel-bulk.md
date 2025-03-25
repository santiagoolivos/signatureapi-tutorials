<!--  Generate Documents and Send in Bulk for Signature in Microsoft Power Automate 


This is the use case for this tutorial:
Sales Proposals: Sales teams can send out multiple contracts or proposals to clients in a single automated workflow.
-->
---
title: Automating Sales Proposals with Excel, SignatureAPI and Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to streamline the sales proposal process by automatically sending, signing, and storing proposals. By integrating **Microsoft Excel** (for storing the proposal data), **OneDrive** (for storing proposal templates), and **SignatureAPI** (for approval-and-signature workflow), you can eliminate manual errors and delays in sending out proposals.

### What You’ll Learn

* How to trigger a flow manually, obtaining the data from an Excel file.
* Retrieving and pre-filling a DOCX proposal template from OneDrive.
* Creating and sending a signature envelope using SignatureAPI.
* Monitoring the signing process and retrieving the signed document.
* Saving the signed proposal and notifying the sales team automatically.

### The Problem

Sales teams often struggle with manually handling proposals, causing delays in sending out proposals. Common issues include:

* **Slow processing** – manual tasks create delays.
* **Errors** – mistakes from manual data entry.
* **Tracking difficulty** – challenges in monitoring signing status.

### How Automation Helps

Automation simplifies this process by:

* Automatically sending proposals to the clients.
* Using templates pre-filled with proposal data.
* Tracking signature status and storing documents automatically.
* Informing the sales team instantly once proposals are signed.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Microsoft Excel** – For storing the proposal data.
* **OneDrive** – For storing the proposal DOCX templates.
* **Outlook** – For sending notifications (other email providers also work).

## Flow Overview

The automation process follows these steps:

1. **Trigger:** Manually trigger the flow.
2. **Data Retrieval:** Get clients proposals data from Excel file.
3. **Signature Process:** Create an envelope via SignatureAPI, add recipient details, and attach the DOCX.
4. **Monitoring:** Wait for the proposal to be signed.
5. **Storage & Notification:** Save the signed document in OneDrive and notify the sales team via email.

Here’s what your final Power Automate flow will look like:

> Screenshot of the completed flow

## Step-by-Step Tutorial

Follow these steps to automate your employment contract process using Microsoft Excel, OneDrive, SignatureAPI, and Microsoft Power Automate:

### Step 1: Prepare the Contract Template

First, create or update your clients proposal template by adding placeholders for dynamic fields (proposal data) and defining where the client will sign.

**To prepare your template:**

1. Open your existing proposal template (DOCX format) in Microsoft Word.
2. Identify each place where proposal data should be dynamically inserted (e.g., company name, contact person, email).
3. Insert placeholders using **double curly brackets** around descriptive keys. Examples:
- Company name: `{{company.name}}`
- Contact person: `{{contact.person}}`
- Contact email: `{{contact.email}}`
4. Define the location for the signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[client_signature]]`

**Example placeholder usage in your document:**

> *Dear `{{company.name}}`,*  
> *Please review and sign your proposal below:*  
> `[[client_signature]]`

5. Save your template.

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

> *Include annotated screenshot clearly indicating examples of placeholders in a DOCX document.*

### Step 2: Create the Excel File

<!-- Once you have created all of your fields, you need to turn your sheet into a table, allowing the table to be selectable in Microsoft Power Automate. You can create a table by doing the following:

Select all of the columns and rows.
In the Ribbon, switch to Insert section and select Table.

You can also name your table by selecting your table, click Table Design in the ribbon, and set the name title.

Once you do that, you will want to save your Excel file to OneDrive or SharePoint, as this will allow you to reference the list in Microsoft Power Automate. -->
First, create an Excel file to store the proposal data.

1. Open Microsoft Excel and create a new blank workbook.
2. Add the following columns:
  - **Company Name** (Text)
  - **Contact Person** (Text)
  - **Contact Email** (Text)
3. Need to turn your sheet into a table, allowing the table to be selectable in Microsoft Power Automate. You can create a table by doing the following:

  - Select all of the columns and rows.
  - In the Ribbon, switch to Insert section and select Table.

  -   You can also name your table by selecting your table, click Table Design in the ribbon, and set the name title.

3. Save the Excel file to OneDrive.

> *Include annotated screenshot highlighting key form creation steps.*

### Step 3: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered manually.

#### 3.1 Configure the Trigger

First, set the flow trigger to run manually.

1. Go to **Power Automate** and select **Instant Cloud Flow**.
2. Add Trigger. Name your flow, select **"Manually trigger a flow"**, and then click **Create**.

> *Include annotated screenshot of trigger configuration.*

#### 3.2 Retrieve Proposal Data from Excel File

Next, retrieve the proposal data from the Excel file.

1. Add the action **"List rows present in a table"** from the Excel connector.
2. Select the **Location** from the Excel file, for **OneDrive for Business**, select the **Document Library** and then select the excel **File**.
3. Select the **Table Name** from the Excel file.


> *Include annotated screenshot showing table retrieval.*



### Step 3: Set Up the Signature Process

In this step, you'll configure SignatureAPI to create, send, and track the signature process.

#### 3.1 Create a SignatureAPI Envelope

Begin by creating an envelope to hold your contract and signature process.

1. Add the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).
3. Set an **Envelope Title** (e.g., employee first name) and a message using dynamic content.

> *Include annotated screenshot of the envelope creation.*

<!-- . Add Step: Get file content. Add the ID from the Get attachment step.

Note: Power Automate will automatically add this to an Apply Each loop. This just means it will loop through all attachments you make to the item. This won’t affect the behavior, as you’re only attaching one document.
 -->
#### 3.2 Get file content

1. Add the **"Get file content"** action from the Sharepoint connector.
2. Select the Site Address.
3. Add to the File Identifier the ID from the **Get attachments** step with Dynamic Content.

Note: Power Automate will automatically add this to an Apply Each loop. This just means it will loop through all attachments you make to the item. This won’t affect the behavior, as you’re only attaching one document (If more attachments ara added to any item, its important that it has the same fields as the template).

> *Include annotated screenshot showing file content action.*


#### 3.3 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with employee details.

1. Add **"Add a Template – DOCX"** action.
2. Select **File Content** from the **Get file content** action.
3. Select the Envelope ID from the **Create an Envelope** action.
4. Set the **Document Title** (e.g., "Employment Contract").
5. Ensure your DOCX template uses placeholders (`{{employee.name}}`, etc.) and map each field to the corresponding dynamic content from your form.

> *Include annotated screenshot demonstrating dynamic content mappings.*

#### 3.4 Define Signature Placement

Specify where the employee should sign on the document.

1. Add **"Add a Place – Signature"** action.
2. Use the placeholder (e.g., `[[employee_signature]]`) from your DOCX template.
3. Ensure the recipient key matches the key defined earlier.
4. Add the **Document ID** from the previous step with Dynamic Content.

> *Include annotated screenshot highlighting signature placement configuration.*


#### 3.5 Add the Recipient

Next, specify who will receive and sign the contract.

1. Outside the **"For each loop"**, add **"Add Recipient"** action from the SignatureAPI connector.
2. Map **Recipient Name** and **Recipient Email** using form details (Dynamic Content).
3. Set the **Recipient Key** (e.g., "employee"), matching your DOCX placeholders.

> *Include annotated screenshot showing recipient details.*

#### 3.6 Start the Signing Process

Trigger the sending of your envelope to the employee for signing.

1. Add **"Start Envelope"** action from the SignatureAPI connector.
2. Select the appropriate **Envelope ID** from dynamic content.

> *Include annotated screenshot confirming the envelope start.*

### Step 4: Monitor and Finalize the Contract

Next, configure your flow to wait for the signing to complete, retrieve the signed contract, and notify HR.

#### 4.1 Wait for Signature Completion

Pause the flow until the employee signs the contract.

1. Add **"Wait for Envelope Completion"** action from the SignatureAPI connector.
2. Select the correct **Envelope ID**.

> *Include annotated screenshot of waiting action.*

#### 4.2 Retrieve the Signed Contract

Once signed, automatically retrieve the completed document.

1. Add **"Get a Deliverable"** action from the SignatureAPI connector.
2. Select the correct **Deliverable ID** from dynamic content.

> *Include annotated screenshot of deliverable retrieval.*

#### 4.3 Save the Signed Contract to Sharepoint

Save the signed document for record-keeping.

1. Add **"Create File"** action (Sharepoint connector).
2. Set the destination folder and filename (ending in `.pdf`).
3. Map **File Content** from the deliverable.

> *Include annotated screenshot of file saving.*

#### 4.4 Notify HR via Email

Automatically inform HR that the contract has been signed and saved.

1. Add **"Send an Email"** action (Outlook connector).
2. Configure the email recipient (HR), subject, and message.
3. Attach the signed contract file from dynamic content.

> *Include annotated screenshot highlighting email notification.*

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