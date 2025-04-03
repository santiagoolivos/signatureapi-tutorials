---
title: Automating Employment Contracts and Storing data in Microsoft Dataverse with Microsoft Forms, SignatureAPI and Microsoft Power Automate.
---

## Overview

This tutorial demonstrates how to streamline the employment contract process by automatically sending, signing, and storing contracts. By integrating **Microsoft Forms** (for data collection), **OneDrive** (for storing contract pdf form), the **SignatureAPI connector** (for electronic signatures with form inputs), and **Microsoft Dataverse** (for storing the employee details from the form and signature completion), you can eliminate manual errors and delays in onboarding new employees.


### What You’ll Learn

* How to trigger a flow with a new Microsoft Forms response.
* Retrieving and pre-filling a DOCX contract template from OneDrive.
* Creating and sending a signature envelope using SignatureAPI for two recipients.
* Capturing data from text input fields in the signature process.
* Monitoring the signing process and retrieving the signed document.
* Saving the signed contract, adding a row to a Dataverse table and notifying HR automatically.

### The Problem

HR departments often struggle with manually handling contracts, causing delays in onboarding. Common issues include:

* **Slow processing** – manual tasks create delays.
* **Errors** – mistakes from manual data entry.
* **Tracking difficulty** – challenges in monitoring signing status.

### How Automation Helps

Automation simplifies this process by:

* Automatically sending contracts upon form submission.
* Using templates pre-filled with employee details.
* Using the Signatureapi form to capture data from the signature process and then add a row to a Dataverse table.
* Tracking signature status and storing documents automatically.
* Informing HR instantly once contracts are signed.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures with form inputs.
* **Microsoft Forms** – For collecting employee information.
* **OneDrive** – For storing your DOCX templates.
* **Microsoft Dataverse** – For storing the employee details from the form and signature completion.
* **Outlook** – For sending notifications (other email providers also work).

## Flow Overview

The automation process follows these steps:

1. **Trigger:** Microsoft Forms submission starts the flow.
2. **Data Retrieval:** Get employee details and fetch the contract template from OneDrive.
3. **Signature Process:** Create an envelope via SignatureAPI, add recipients details, and attach the DOCX.
4. **Monitoring:** Wait for the contract to be signed.
5. **Storage & Notification:** Save the signed document in OneDrive, add a row to a Dataverse table and notify HR via email.

Here’s what your final Power Automate flow will look like:

> Screenshot of the completed flow

## Step-by-Step Tutorial

Follow these steps to automate your employment contract process using Microsoft Forms, Microsoft Dataverse, SignatureAPI, and Power Automate:

### Step 1: Prepare the Contract Template

First, create or update your employment contract template by adding placeholders for dynamic fields (employee details), defining the employer and employee signature fields, and adding a place for the salary amount that the employer will fill during the signature process.

**To prepare your template:**

1. Open your existing employment contract document (DOCX format) in Microsoft Word.
2. Identify each place where employee details should be dynamically inserted (e.g., name, email).
3. Insert placeholders using **double curly brackets** around descriptive keys. Examples:
- Employee first name: `{{employee.first_name}}`
- Employee last name: `{{employee.last_name}}`
- Employee email: `{{employee.email}}`
4. Define the location for the employee signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[employee_signature]]`
5. Define the location for the employer signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[employer_signature]]`
6. Add a place for the salary amount that the employer will fill during the signature process, e.g.:  `[[salary_input]]`

**Example placeholder usage in your document:**

> *Dear `{{employee.first_name}}` `{{employee.last_name}}`,*  

> *This will be the salary amount you will receive per month: `[[salary_input]]`*

> *Please review and sign your employment contract below:*  
> `[[employee_signature]]`
> `[[employer_signature]]`
5. Save your template and upload it to **OneDrive** (or another preferred storage service).

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

![Word Template Example](/images/word/employee-template-salary.png)


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

### Step 3: Create the Dataverse Table

Create a Dataverse table to store the employee details from the form and signature completion.

1. Go to [Power Apps](https://make.powerapps.com) and sign in.
2. From the left menu click on **"Tables"**.
3. Click on the **Start with a blank table** button.

    ![Start with a blank table](/images/dataverse/start-with-blank.png)
      
3. Edit the New column and rename it to **"First Name"**. Click on the column name, then in **Edit column** and rename it from the **Display name** field.
     
    ![Rename column](/images/dataverse/rename-column.png)

4. Add the following columns:
    - **Last Name** (Text)
    - **Email Address** (Text)
    - **Salary Amount** (Text)
    - **Signature Completion** (Text)

    ![Add columns](/images/dataverse/add-columns.png)

5. Rename the table from the **Properties** button (e.g. **Employee Contracts**) and click on the **Save and exit** button.

    ![Rename table](/images/dataverse/rename-table.png)

### Step 4: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, triggered whenever a form is submitted.

#### 4.1 Configure the Trigger

First, set the flow trigger to run whenever your form is submitted.

1. Go to **Power Automate** and select **Automated Cloud Flow**.
2. Choose the trigger **"When a new response is submitted"** (Microsoft Forms).

    ![Trigger](/images/powerautomate/dataverse-flow/trigger.png)
    
3. Select the form you created earlier.

    ![Select form](/images/powerautomate/dataverse-flow/select-form.png)

#### 4.2 Retrieve Employee Details

Next, retrieve the employee details submitted through the form.

1. Add the action **"Get response details"**.
2. Select your form (**Form ID**) and the response (**Response ID**) from Dynamic Content.

    ![Get response details](/images/powerautomate/dataverse-flow/get-response-details.png)

#### 4.3 Retrieve Contract Template from OneDrive

Then, fetch your employment contract template stored in OneDrive.

1. Add **"Get File Content using Path"** from the OneDrive connector.
2. Select the DOCX template stored in your OneDrive.

    ![Get file content](/images/powerautomate/dataverse-flow/get-file-content.png)

### Step 4: Set Up the Signature Process

In this step, you'll configure SignatureAPI to create, send, and track the signature process.

#### 4.1 Create a SignatureAPI Envelope

Begin by creating an envelope to hold your contract and signature process.

1. Add the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).
3. Set an **Envelope Title** (e.g., employee name) and email message using dynamic content.
4. From the advanced parameters, select the **"Envelope Routing"** and set that to **"sequential"**. This will ensure that the envelope is sent to the employer first, and then to the employee.

    ![Envelope routing](/images/powerautomate/dataverse-flow/create-envelope.png)

#### 4.2 Add the Recipient - Employer

Next, specify who will receive and sign the contract.

1. Add **"Add Recipient"** action and rename it to **"Add Recipient - Employer"**.
2. Set the **Recipient Name** and **Recipient Email**  of the employer (e.g. "John Doe" and "john.doe@example.com").
3. Set the employer **Recipient Key** (e.g., "employer"), matching your DOCX placeholders.

    ![Add recipient employer](/images/powerautomate/dataverse-flow/add-employer.png)

#### 4.3 Add the Recipient - Employee

Next, specify who will receive and sign the contract.

1. Add **"Add Recipient"** action and rename it to **"Add Recipient - Employee"**.
2. Set the **Recipient Name** and **Recipient Email**  using form details (Dynamic Content).
3. Set the employee **Recipient Key** (e.g., "employee"), matching your DOCX placeholders.

    ![Add recipient employee](/images/powerautomate/dataverse-flow/add-employee.png)

#### 4.4 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with employee details.

1. Add **"Add a Template – DOCX"** action.
2. Select **File Content** from the OneDrive action.
3. Set the **Document Title** (e.g., "Employment Contract").
4. Ensure your DOCX template uses placeholders (`{{employee.first_name}}`, `{{employee.last_name}}`, `{{employee.email}}`, etc.) and map each field to the corresponding dynamic content from your form.

    ![Add template](/images/powerautomate/dataverse-flow/add-template.png)

#### 4.5 Define Signature Placement - Employer

Specify where the employer should sign on the document.

1. Add **"Add a Place – Signature"** action and rename it to **"Add a Place – Employer Signature"**.
2. Use the placeholder (e.g., `[[employer_signature]]`) from your DOCX template.
3. Set the **Recipient Key** from the **Add Recipient - Employer** action.

    ![Add place employer](/images/powerautomate/dataverse-flow/add-employer-signature.png)

#### 4.6 Define Text Input Placement - Salary Input

Specify where the employer should fill the salary amount.

1. Add **"Add a Place – Text Input"** action and rename it to **"Add a Place – Salary Input"**.
2. Use the placeholder (e.g., `[[salary_input]]`) from your DOCX template.
3. Set the **Recipient Key** from the **Add Recipient - Employer** action. This will ensure that the salary amount is defined and completed in the signature process by the employer.
4. From the advanced parameters, set the **"Capture As"** as "salary_input". This ensures that the salary amount is captured as text, and be able to get it later in the flow.

    ![Add place salary](/images/powerautomate/dataverse-flow/add-salary-input.png)

#### 4.7 Define Signature Placement - Employee

Specify where the employer should sign on the document.

1. Add **"Add a Place – Signature"** action and rename it to **"Add a Place – Employee Signature"**.
2. Use the placeholder (e.g., `[[employee_signature]]`) from your DOCX template.
3. Ensure the recipient key matches the key defined earlier.

    ![Add place employee](/images/powerautomate/dataverse-flow/add-employee-signature.png)

#### 4.8 Start the Signing Process

Trigger the sending of your envelope to the employee for signing.

1. Add **"Start Envelope"** action.
2. Select the appropriate **Envelope ID** from dynamic content.

    ![Start envelope](/images/powerautomate/dataverse-flow/start-envelope.png)

### Step 5: Monitor and Finalize the Contract

Next, configure your flow to wait for the signing to complete, retrieve the signed contract, and notify HR.

#### 5.1 Wait for Signature Completion

Pause the flow until the employee signs the contract.

1. Add **"Wait for Envelope Completion"** action.
2. Select the correct **Envelope ID**.

    ![Wait for envelope completion](/images/powerautomate/dataverse-flow/wait-envelope.png)

#### 5.2 Get captured value

Once signed, automatically retrieve the captured value from the employee salary.

1. Add **"Get a captured value"** action from the SignatureAPI connector.
2. Select the correct **Envelope ID** from dynamic content.
3. Select the correct **Captured Key**, must be the same as the one defined earlier ("salary_input") to be able to get the salary amount filled by the employer.

    ![Get captured value](/images/powerautomate/dataverse-flow/get-captured-value.png)


#### 5.3 Add a row to dataverse

Now, add a row to the Dataverse table with the employee details and the captured salary amount.

1. Add **"Add a new row"** action from the Dataverse connector.
2. Select the correct **Dataverse Table**, must be the same as the one defined earlier.
3. From the **Advanced parameters** select the **"Fields"** to be mapped to the employee details and the captured salary amount from the dynamic content.
    
    - The **First Name**, **Last Name** and **Email Address** will be mapped from the form response details.
    - The **Salary Amount** will be mapped from the captured value from the signature process.
    - The **Signature Completion** will be mapped from the **Wait for Envelope Completion** action.

    ![Add row](/images/powerautomate/dataverse-flow/add-dataverse-row.png)


#### 5.4 Retrieve the Signed Contract

Retrieve the completed document.

1. Add **"Get a Deliverable"** action.
2. Select the correct **Deliverable ID** from dynamic content.

    ![Get deliverable](/images/powerautomate/dataverse-flow/get-deliverable.png)

#### 5.5 Save the Signed Contract to OneDrive

Save the signed document for record-keeping.

1. Add **"Create File"** action (OneDrive connector).
2. Set the destination folder and filename (ending in `.pdf`).
3. Map **File Content** from the deliverable.

    ![Save file](/images/powerautomate/dataverse-flow/save-file.png)

#### 5.6 Notify HR via Email

Automatically inform HR that the contract has been signed and saved.

1. Add **"Send an Email"** action (Outlook connector).
2. Configure the email recipient (HR), subject, and message.
3. Attach the signed contract file from dynamic content. Ensure to use the **"File Content"** from the **"Get a Deliverable"** action, and filename (ending in `.pdf`).

    ![Send email](/images/powerautomate/dataverse-flow/send-email.png)

### Step 6: Test Your Automation

Finally, test the entire process end-to-end.

1. Save your Power Automate flow.
2. Submit a test response through your Microsoft Form.
3. Verify:
  - The form is submitted successfully.
  - The flow is triggered and the contract is sent to the employer.
  - After the employer fills the salary amount and signs the contract, the employee receives the contract and signs it.
  - A row is added to the Dataverse table with the employee details and the captured salary amount.
  - The signed contract is saved successfully in OneDrive.
  - HR receives an email notification with the signed contract attached.

*Use the following checklist:*

- [ ] Contract sent successfully.
- [ ] Employer fills the salary amount and signs the contract.
- [ ] Employee receives and signs the contract.
- [ ] A row is added to the Dataverse table with the employee details and the captured salary amount.
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
- [Microsoft Dataverse Documentation](https://www.microsoft.com/en-us/power-platform/dataverse)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you've successfully automated the process of sending, signing, and managing employment contracts. This efficient workflow frees your HR team from repetitive tasks and ensures new employees have a smooth onboarding experience.

**Happy automating!**