---
title: Automating Sales Proposals with Excel, SignatureAPI and Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to streamline the clients sales proposal process by automatically sending, signing, and storing proposals. By integrating **Microsoft Excel** (for storing the proposal data), **OneDrive** (for storing proposal templates), and **SignatureAPI** (for approval-and-signature workflow), you can eliminate manual errors and delays in sending out proposals.

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

This tutorial has two flows, this is the first flow that will create the envelopes and send the proposals to the clients. 

![Flow](/images/powerautomate/excel-flow/complete-flow-1.png)

The second flow will monitor the signing process and notify the sales team.

![Flow](/images/powerautomate/excel-flow/complete-flow-2.png)


## Step-by-Step Tutorial

Follow these steps to automate your employment contract process using Microsoft Excel, OneDrive, SignatureAPI, and Microsoft Power Automate:

### Step 1: Prepare the Contract Template

First, create or update your clients proposal template by adding placeholders for dynamic fields (proposal data) and defining where the client will sign.

**To prepare your template:**

1. Open your existing proposal template (DOCX format) in Microsoft Word.
2. Identify each place where proposal data should be dynamically inserted (e.g., client name, contact person, email).
3. Insert placeholders using **double curly brackets** around descriptive keys. Examples:
- Client name: `{{client.name}}`
- Contact name: `{{contact.name}}`
- Contact email: `{{contact.email}}`
4. Define the location for the signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[client_signature]]`

**Example placeholder usage in your document:**

> *Dear `{{client.name}}`,*  
> *Please review and sign your proposal below:*  
> `[[client_signature]]`

5. Save your template.

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

![Word Template Example](/images/word/client-sales.png)

### Step 2: Create the Excel File

First, create an Excel file to store the proposal data.

1. Open Microsoft Excel and create a new blank workbook.
![Excel Template](/images/excel/new-excel.png)
2. Add the following columns:
  - **Client Name** (Text)
  - **Contact Name** (Text)
  - **Contact Email** (Text)
3. Need to turn your sheet into a table, allowing the table to be selectable in Microsoft Power Automate. You can create a table by doing the following:

  - Select all of the columns and rows.
  - In the Ribbon, switch to Insert section and select Table.
  ![Excel Template](/images/excel/create-table.png)
  - You can also name your table by selecting your table, click Table Design in the ribbon, and set the name title.
    ![Excel Template](/images/excel/rename-table.png)
3. Save the Excel file to OneDrive.

### Step 3: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered manually.

#### 3.1 Configure the Trigger

First, set the flow trigger to run manually.

1. Go to **Power Automate** and select **Instant Cloud Flow**.
2. Add Trigger. Name your flow, select **"Manually trigger a flow"**, and then click **Create**.

    ![Trigger](/images/powerautomate/excel-flow/trigger.png)

#### 3.2 Retrieve Proposal Data from Excel File

Next, retrieve the proposal data from the Excel file.

1. Add the action **"List rows present in a table"** from the Excel connector.
2. Select the **Location** from the Excel file, for **OneDrive for Business**, select the **Document Library** and then select the excel **File**.
3. Select the **Table Name** from the Excel file.

    ![List Rows](/images/powerautomate/excel-flow/list-rows.png)

#### 3.3 Initialize a variable

In this step, you'll initialize a variable to store the proposal data.

1. Add the action **"Initialize Variable"** from the Variables connector.
2. Set the **Name** to the variable name (e.g., clientsData).
3. Set **Type** to Array.
4. Set **Value** to the proposal data from the **"List rows present in a table"** action.

    ![Initialize Variable](/images/powerautomate/excel-flow/initialize-variable.png)

#### 3.4 Get file content

1. Add the **"Get file content using path"** action from the OneDrive for Business connector.
2. Select the File path to the proposal template.

    ![Get File Content](/images/powerautomate/excel-flow/get-file-content.png)

#### 3.5 Apply each loop

Now, in this step you will add the **"Apply to each"** action to the flow, that will allow you to iterate through each of the rows in the flow.

1. Add the **"Apply each"** action from the Control connector.
2. Set the **Items** to the proposal data from the **"List rows present in a table"** action from dynamic content.

    ![Apply Each](/images/powerautomate/excel-flow/apply-each.png)

### Step 4: Set Up the Signature Process

In this step, you'll configure SignatureAPI to create, send, and track the signature process for each proposal.

#### 4.1 Create a SignatureAPI Envelope

Inside the **"Apply each loop"**, begin by creating an envelope to hold your proposal and signature process.

1. Add the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).
3. Set an **Envelope Title** (e.g., proposal) and a message using dynamic content.
4. In the advanced options, select the topics, and set the topic that this envelopes will have in common (e.g., "clients_proposals"). This will be useful later, to monitor the signing process.

    ![Create envelope](/images/powerautomate/excel-flow/create-envelope.png)

#### 4.2 Add the Recipient

Next, specify who will receive and sign the contract.

1. Add **"Add Recipient"** action from the SignatureAPI connector.
2. Map **Recipient Name** and **Recipient Email** using form details (Dynamic Content).
3. Set the **Recipient Key** (e.g., "client"), matching your DOCX placeholders.

    ![Add recipient](/images/powerautomate/excel-flow/add-recipient.png)


#### 4.3 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with proposal details.

1. Add **"Add a Template – DOCX"** action.
2. Select **File Content** from the OneDrive action.
3. Select the Envelope ID from the **Create an Envelope** action.
4. Set the **Document Title** (e.g., "Client Name Proposal").
5. Ensure your DOCX template uses placeholders (`{{client.name}}`, etc.) and map each field to the corresponding dynamic content from your form.

    ![Add template](/images/powerautomate/excel-flow/add-template.png)

#### 4.4 Define Signature Placement

Specify where the client should sign on the document.

1. Add **"Add a Place – Signature"** action.
2. Use the placeholder (e.g., `[[client_signature]]`) from your DOCX template.
3. Ensure the recipient key matches the key defined earlier.
4. Add the **Document ID** from the previous step with Dynamic Content.

    ![Add signature](/images/powerautomate/excel-flow/add-signature.png)


#### 4.5 Start the Signing Process

Trigger the sending of your envelope to the client for signing.

1. Add **"Start Envelope"** action from the SignatureAPI connector.
2. Select the appropriate **Envelope ID** from dynamic content.
3. Save the flow.

    ![Start envelope](/images/powerautomate/excel-flow/start-envelope.png)

### Step 5: Monitor and Finalize the Contract

Next, configure your flow to wait for the signing to complete, retrieve the signed contract, and notify the sales team.

It is necessary to create a new automated flow to monitor the signing process.

#### 5.1 Create a new flow to monitor the signing process

Now, create a new **"Automated cloud flow"** to monitor the signing process.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2.  Name your flow, select **"When a deliverable is generated"** from the SignatureAPI connector.
    
    ![Create flow](/images/powerautomate/excel-flow/when-deli-generated.png)

3. Select the topics item that will filter the deliverables that this flow will process. This should be the same topic as the one used in the previous flow defined in the **4.1 Create a SignatureAPI Envelope step**.

    ![Select topic](/images/powerautomate/excel-flow/webhook-topics.png)

#### 5.2 Retrieve the Signed Contract

Retrieve the signed contract from the SignatureAPI.

1. Add **"Get a Deliverable"** action from the SignatureAPI connector.
2. Select the correct **Deliverable ID** from dynamic content.

    ![Get deliverable](/images/powerautomate/excel-flow/get-deliverable.png)

#### 5.3 Save the Signed Contract to OneDrive

Save the signed document for record-keeping.

1. Add **"Create File"** action (OneDrive connector).
2. Set the folder path and filename (ending in `.pdf`).
3. Map **File Content** from the deliverable.

    ![Save file](/images/powerautomate/excel-flow/onedrive-create-file.png)

#### 5.4 Notify the sales team via Email

Automatically inform the sales team that the proposal has been signed and saved.

1. Add **"Send an Email"** action (Outlook connector).
2. Configure the email recipient (sales team), subject, and message.
3. Attach the signed proposal file from dynamic content.
  - Add the name of the attachment with a ".pdf" extension.
  - Add the signed proposal file from dynamic content.
4. Save the flow.

    ![Send email](/images/powerautomate/excel-flow/outlook-notify.png)

### Step 6: Test Your Automation

Finally, test the entire process end-to-end.

1. Manually trigger the flow.
2. Verify:
  - Proposal is sent to all the clients in the Excel file.
  - Signature process initiates correctly.
3. After the signature process is completed, verify:
  - Signed proposal saves successfully in OneDrive.
  - Sales team receives an email notification with the signed proposal attached.

*Use the following checklist:*

- [ ] Proposal sent successfully.
- [ ] Client receives and signs proposal.
- [ ] Signed proposal stored correctly in OneDrive.
- [ ] Sales team receives email notification with attachment.

## Troubleshooting & FAQ

### Common Issues:

- **API Key Errors:** Ensure your SignatureAPI key is correct and authenticated.
- **Dynamic Content Mapping:** Double-check placeholder naming in your DOCX file matches exactly with dynamic content mappings.
- **File Access Issues:** Verify permissions and file paths in OneDrive.

### Frequently Asked Questions:

- *What if the proposal isn't sent?*  
  Check your SignatureAPI dashboard for errors, and verify recipient details.

- *Can I adapt this for other document types?*  
  Yes, this method is adaptable for any automated document-signing workflow.

## Best Practices & Security

- Always securely manage API keys.
- Regularly check flow runs in Power Automate for any errors.
- Document any flow or template changes for future reference.

## Additional Resources

- [SignatureAPI Documentation](https://signatureapi.com/docs)
- [Microsoft OneDrive Documentation](https://www.microsoft.com/en-us/microsoft-365/onedrive/online-cloud-storage)
- [Microsoft Excel Documentation](https://www.microsoft.com/en-us/microsoft-365/excel)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of sending, signing, and managing sales proposals. This efficient workflow frees your sales team from repetitive tasks and ensures new clients have a smooth onboarding experience.

**Happy automating!**