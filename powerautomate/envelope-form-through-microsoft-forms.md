---
title: Automating Employment Contracts with Microsoft Forms, SignatureAPI, and Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to streamline the employment contract process by automatically sending, signing, and storing contracts. By integrating **Microsoft Forms** (for data collection), **OneDrive** (for storing contract templates), and the **SignatureAPI connector** (for electronic signatures), you can eliminate manual errors and delays in onboarding new employees.

### What You’ll Learn

* How to trigger a flow with a new Microsoft Forms response.
* Retrieving and pre-filling a DOCX contract template from OneDrive.
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

* Automatically sending contracts upon form submission.
* Using templates pre-filled with employee details.
* Tracking signature status and storing documents automatically.
* Informing HR instantly once contracts are signed.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Microsoft Forms** – For collecting employee information.
* **OneDrive** – For storing your DOCX templates.
* **Outlook** – For sending notifications (other email providers also work).

## Flow Overview

The automation process follows these steps:

1. **Trigger:** Microsoft Forms submission starts the flow.
2. **Data Retrieval:** Get employee details and fetch the contract template from OneDrive.
3. **Signature Process:** Create an envelope via SignatureAPI, add recipient details, and attach the DOCX.
4. **Monitoring:** Wait for the contract to be signed.
5. **Storage & Notification:** Save the signed document in OneDrive and notify HR via email.

Here’s what your final Power Automate flow will look like:
![Flow](/images/powerautomate/forms-flow/complete-flow.png)

## Step-by-Step Tutorial

Follow these steps to automate your employment contract process using Microsoft Forms, SignatureAPI, and Power Automate:

### Step 1: Prepare the Contract Template

First, create or update your employment contract template by adding placeholders for dynamic fields (employee details) and defining where the employee will sign.

**To prepare your template:**

1. Open your existing employment contract document (DOCX format) in Microsoft Word.
2. Identify each place where employee details should be dynamically inserted (e.g., name, email).
3. Insert placeholders using **double curly brackets** around descriptive keys. Examples:
- Employee first name: `{{employee.first_name}}`
- Employee last name: `{{employee.last_name}}`
- Employee email: `{{employee.email}}`
4. Define the location for the signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[employee_signature]]`

**Example placeholder usage in your document:**

> *Dear `{{employee.first_name}}` `{{employee.last_name}}`,*  
> *Please review and sign your employment contract below:*  
> `[[employee_signature]]`

5. Save your template and upload it to **OneDrive** (or another preferred storage service).

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

![Word Template Example](/images/word/employee-template.png)

### Step 2: Create the Microsoft Form

First, create a Microsoft Form to collect necessary employee details (First Name, Last Name, Email Address).

1. Visit [Microsoft Forms](https://forms.office.com) and sign in.
2. Click **"New Form"**.

    ![New Form](/images/forms/new-form.png)
3. Name the form and add these required questions:
  - **First Name** (Text, required)
  - **Last Name** (Text, required)
  - **Email Address** (Text, required)

    ![Add request](/images/forms/add-text-request.png)

    ![Rename form](/images/forms/rename-form.png)

4. Save and publish your form.

    ![Employee Form](/images/forms/employee-form.png)

### Step 3: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered whenever a form is submitted.

#### 3.1 Configure the Trigger

First, set the flow trigger to run whenever your form is submitted.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Choose the trigger **"When a new response is submitted"** (Microsoft Forms).

    ![Trigger](/images/powerautomate/forms-flow/trigger.png)
    
3. Select the form you created earlier.

    ![Select form](/images/powerautomate/forms-flow/select-form.png)


#### 3.2 Retrieve Employee Details

Next, retrieve the employee details submitted through the form.

1. Add the action **"Get response details"**.
2. Select your form (**Form ID**) and the response (**Response ID**) from Dynamic Content.

   ![Get response details](/images/powerautomate/forms-flow/get-response-details.png)
#### 3.3 Retrieve Contract Template from OneDrive

Then, fetch your employment contract template stored in OneDrive.

1. Add **"Get File Content using Path"** from the OneDrive connector.
2. Select the DOCX template stored in your OneDrive.

    ![Get file content](/images/powerautomate/forms-flow/get-file-content.png)

### Step 4: Set Up the Signature Process

In this step, you'll configure SignatureAPI to create, send, and track the signature process.

#### 4.1 Create a SignatureAPI Envelope

Begin by creating an envelope to hold your contract and signature process.

1. Add the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).
3. Set an **Envelope Title** (e.g., employee name) and email message using dynamic content.

    ![Create envelope](/images/powerautomate/forms-flow/create-envelope.png)

#### 4.2 Add the Recipient

Next, specify who will receive and sign the contract.

1. Add **"Add Recipient"** action.
2. Map **Recipient Name** and **Recipient Email** using form details (Dynamic Content).
3. Set the **Recipient Key** (e.g., "employee"), matching your DOCX placeholders.

    ![Add recipient](/images/powerautomate/forms-flow/add-recipient.png)

#### 4.3 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with employee details.

1. Add **"Add a Template – DOCX"** action.
2. Select **File Content** from the OneDrive action.
3. Set the **Document Title** (e.g., "Employment Contract").
4. Ensure your DOCX template uses placeholders (`{{employee.first_name}}`, etc.) and map each field to the corresponding dynamic content from your form.

    ![Add template](/images/powerautomate/forms-flow/add-docx-template.png)

#### 4.4 Define Signature Placement

Specify where the employee should sign on the document.

1. Add **"Add a Place – Signature"** action.
2. Set the **Document ID** using dynamic content.
3. Use the placeholder (e.g., `[[employee_signature]]`) from your DOCX template.
4. Set the **Recipient Key** using dynamic content.

    ![Add signature](/images/powerautomate/forms-flow/add-signature.png)

#### 4.5 Start the Signing Process

Trigger the sending of your envelope to the employee for signing.

1. Add **"Start Envelope"** action.
2. Select the appropriate **Envelope ID** using dynamic content.

    ![Start envelope](/images/powerautomate/forms-flow/start-envelope.png)

### Step 5: Monitor and Finalize the Contract

Next, configure your flow to wait for the signing to complete, retrieve the signed contract, and notify HR.

#### 5.1 Wait for Signature Completion

Pause the flow until the employee signs the contract.

1. Add **"Wait for Envelope Completion"** action.
2. Select the correct **Envelope ID** using dynamic content.

    ![Wait for envelope completion](/images/powerautomate/forms-flow/wait-envelope.png)

#### 5.2 Retrieve the Signed Contract

Once signed, automatically retrieve the completed document.

1. Add **"Get a Deliverable"** action.
2. Select the correct **Deliverable ID** using dynamic content.

    ![Get deliverable](/images/powerautomate/forms-flow/get-deliverable.png)

#### 5.3 Save the Signed Contract to OneDrive

Save the signed document for record-keeping.

1. Add **"Create File"** action (OneDrive connector).
2. Set the folder path and filename (ending in `.pdf`).
3. Map **File Content** from the deliverable.

    ![Save file](/images/powerautomate/forms-flow/save-file.png)

#### 5.4 Notify HR via Email

Automatically inform HR that the contract has been signed and saved.

1. Add **"Send an Email"** action (Outlook connector).
2. Configure the email recipient (HR), subject, and message.
3. Attach the signed contract file from dynamic content. Ensure to use the **"File Content"** from the **"Get a Deliverable"** action, and filename (ending in `.pdf`).

    ![Send email](/images/powerautomate/forms-flow/notify-outlook.png)

### Step 6: Test Your Automation

Finally, test the entire process end-to-end.

1. Save your Power Automate flow.
2. Submit a test response through your Microsoft Form.
3. Verify:
  - Contract is sent to the employee.
  - Signature process initiates correctly.
  - Signed contract saves successfully in OneDrive.
  - HR receives an email notification with the signed contract attached.

*Use the following checklist:*

- [ ] Contract sent successfully.
- [ ] Employee receives and signs contract.
- [ ] Signed document stored correctly in OneDrive.
- [ ] HR receives email notification with attachment.

## Troubleshooting & FAQ

### Common Issues:

- **API Key Errors:** Ensure your SignatureAPI key is correct and authenticated.
- **Dynamic Content Mapping:** Double-check placeholder naming in your DOCX file matches exactly with dynamic content mappings.
- **File Access Issues:** Verify permissions and file paths in OneDrive.

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
- [Microsoft Forms Help](https://support.microsoft.com/forms)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of sending, signing, and managing employment contracts. This efficient workflow frees your HR team from repetitive tasks and ensures new employees have a smooth onboarding experience.

**Happy automating!**