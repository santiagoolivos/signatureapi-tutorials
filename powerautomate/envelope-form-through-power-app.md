---
title: Automating Customer Agreements Signing Process with Power Apps, SignatureAPI and Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to streamline the customer agreements signing process by automatically sending, signing, and storing agreements. By integrating **Power Apps** (for data collection), **SharePoint Documents** (for storing contract templates), and the **SignatureAPI connector** (for electronic signatures), you can eliminate manual errors and delays in customer agreements signing.

### What You’ll Learn

* How to trigger a flow with a new Power App response.
* Retrieving and pre-filling a DOCX contract template from SharePoint.
* Creating and sending a signature envelope using SignatureAPI.
* Monitoring the signing process and retrieving the signed document.
* Saving the signed contract automatically notifying the business.

### The Problem

Businesses often struggle with manually handling customer agreements, causing delays in customer onboarding. Common issues include:

* **Slow processing** – manual tasks create delays.
* **Errors** – mistakes from manual data entry.
* **Tracking difficulty** – challenges in monitoring signing status.

### How Automation Helps

Automation simplifies this process by:

* Automatically sending agreements upon submission of a Power App form.
* Using templates pre-filled with customer details.
* Tracking signature status and storing documents automatically.
* Instantly informing the business once agreements are signed.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Power Apps** – For collecting customer information.
* **SharePoint Documents** – For storing your DOCX templates.
* **Outlook** – For sending notifications (other email providers also work).

## Flow Overview

The automation process follows these steps:

1. **Trigger:** Power Apps submission starts the flow.
2. **Data Retrieval:** Get customer details and fetch the agreement template from SharePoint Documents.
3. **Signature Process:** Create an envelope via SignatureAPI, add recipient details, and attach the DOCX.
4. **Monitoring:** Wait for the agreement to be signed.
5. **Storage & Notification:** Save the signed document in SharePoint and notify the business via email.

Here’s what your final Power Automate flow will look like:

![Flow](/images/powerapps/complete-flow.png)

## Step-by-Step Tutorial

Follow these steps to automate your customer agreements signing process using Power Apps, SignatureAPI, and Microsoft Power Automate:

### Step 1: Prepare the Agreement Template

First, create or update your agreement template by adding placeholders for dynamic fields (customer details) and defining where the customer will sign.

**To prepare your template:**

1. Open your existing customer agreement document (DOCX format) in Microsoft Word.
2. Identify each place where customer details should be dynamically inserted (e.g., name, email).
3. Insert placeholders using **double curly brackets** around descriptive keys. Examples:
- Customer name: `{{customer.name}}`
- Customer email: `{{customer.email}}`
4. Define the location for the signature clearly by inserting a signature placeholder using **double square brackets**, e.g.:  `[[customer_signature]]`

**Example placeholder usage in your document:**

> *Dear `{{customer.name}}`,*  
> *Please review and sign your agreement below:*  
> `[[customer_signature]]`

5. Save your template and upload it to **Documents** in the **SharePoint Site**.

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

![Word Template Example](/images/word/customer-agreement.png)



### Step 2: Create a Power App and Initialize Variables

First, create a Power App to collect necessary customer details (Name, Email Address).

1. Visit [Power Apps](https://make.powerapps.com) and sign in.
2. Create a new **Blank Canvas App** and choose the format type. For this example, choose the **Phone** format.

    ![New Power App](/images/powerapps/new-power-app.png)

3. From the left-hand menu bar, click on the three dots, select **Power Automate**, and select **Create New Flow**.

    ![New Power Automate Flow](/images/powerapps/new-powerautomate-flow.png)
4. Select **Create From Blank**.
5. Enter a name for the Flow. Notice that Power Apps has automatically been selected to trigger the Flow.

    ![Trigger](/images/powerapps/trigger.png)

6. Click on the trigger (Power Apps (V2)) and select **Add an input**. 

    ![Add Input](/images/powerapps/add-input.png)
7. Select the **Text** type, and enter a name for the input (for example, Customer Name). Do it also for the other variable (Customer Email).

    ![Text Input](/images/powerapps/pau-text-input.png)
8. Add a new step and Select the variable action Initialize Variable.

  - Select the three dots and Rename this step according to the variable name (for example, Customer Name) before filling out any of the required fields. This will properly name the variable’s Dynamic Content value later on. If the step is not renamed, the value will automatically be named as “InitializeVariable_Value.”
  - Enter the variable Name.
  - Use the Type drop-down menu and select String as the variable type.
  - Click in the Value field and select the Customer Name variable from the Dynamic Content list. This will allow us to pass in a parameter associated with this variable in Power Apps.
  - Repeat these steps to initialize the other variable (Customer Email).

    ![Initialize Variable](/images/powerapps/pau-initialize-variable.png)

### Step 3: Retrieve Contract Template from SharePoint


Now, fetch your customer agreement template stored in SharePoint.

1. Add **"Get File Content using Path"** from the SharePoint connector.
2. Select the DOCX template stored in your SharePoint.

    ![Get File Content](/images/powerapps/pau-get-file-content.png)

### Step 4: Create the Envelope with SignatureAPI 

In this step, you'll configure SignatureAPI to create, send, and track the signature process.

#### 4.1 Create a SignatureAPI Envelope

Begin by creating an envelope to hold your contract and signature process.

1. Add a new step with the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).
3. Set an **Envelope Title** (e.g., customer name) and email message using dynamic content.

    ![Create envelope](/images/powerapps/create-envelope.png)

#### 4.2 Add the Recipient

Next, specify who will receive and sign the contract.

1. Add **"Add Recipient"** action.
2. Map **Recipient Name** and **Recipient Email** using form details (Dynamic Content).
3. Set the **Recipient Key** (e.g., "customer"), matching your DOCX placeholders.

    ![Add recipient](/images/powerapps/add-recipient.png)

#### 4.3 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with customer details.

1. Add **"Add a Template – DOCX"** action.
2. Select **File Content** from the SharePoint action.
3. Set the **Document Title** (e.g., "Customer Agreement").
4. Ensure your DOCX template uses placeholders (`{{customer.name}}`, etc.) and map each field to the corresponding dynamic content from the Power Apps initialized variables.

    ![Add template](/images/powerapps/add-template.png)

#### 4.4 Define Signature Placement

Specify where the customer should sign on the document.

1. Add **"Add a Place – Signature"** action.
2. Set the **Document ID** using dynamic content.
3. Use the placeholder (e.g., `[[customer_signature]]`) from your DOCX template.
4. Set the **Recipient Key** using dynamic content.

    ![Add signature](/images/powerapps/add-signature.png)

#### 4.5 Start the Signing Process

Trigger the sending of your envelope to the customer for signing.

1. Add **"Start Envelope"** action.
2. Select the appropriate **Envelope ID** using dynamic content.

    ![Start envelope](/images/powerapps/start-envelope.png)

### Step 5: Monitor and Finalize the Contract

Next, configure your flow to wait for the signing to complete, retrieve the signed contract, and notify the business.

#### 5.1 Wait for Signature Completion

Pause the flow until the customer signs the contract.

1. Add **"Wait for Envelope Completion"** action.
2. Select the correct **Envelope ID** using dynamic content.

    ![Wait for envelope](/images/powerapps/wait-envelope.png)

#### 5.2 Retrieve the Signed Contract

Once signed, automatically retrieve the completed document.

1. Add **"Get Deliverables"** action.
2. Select the correct **Deliverable ID** using dynamic content.

    ![Get deliverable](/images/powerapps/get-deliverable.png)

#### 5.3 Save the Signed Contract to SharePoint

Save the signed document for record-keeping.

1. Add **"Create File"** action (SharePoint connector).
2. Select the **Site Address** and **Folder Path**.
3. Set the **File Name** (ending in `.pdf`).
4. Map **File Content** from the deliverable.

    ![Save file](/images/powerapps/save-file.png)

#### 5.4 Notify HR via Email

Automatically inform HR that the contract has been signed and saved.

1. Add **"Send an Email"** action (Outlook connector).
2. Configure the email recipient (business), subject, and message.
3. Attach the signed contract file from dynamic content.

    ![Send email](/images/powerapps/send-email.png)

<!-- save the flow   -->
#### 5.5 Save the Flow

Save the flow clicking on the **Save** button in the top left corner. This will save the flow to Power Automate and add it to the Power App.


### Step 6: Create the text inputs in power app

Create the text inputs in the Power App to collect the customer details.

1. Select Insert in the top menu bar and then select Label.
2. Change the Text value of this label to "Name:” This can be done in either the top function bar or through the Text property in the right-hand menu.

    ![Label](/images/powerapps/add-label.png)

3. Select Input from the top menu bar and select Text input from the drop-down menu.

  - Repeat these steps to create the other text input fields for the other variable (Customer Email).

    **Quick Tip!** Select the text input bar. Notice the element name has been highlighted in the left-hand Tree view. Click the three dots on this element and Rename this element to match the text label. In this case, "Name." This will enable you to identify this element easily later on when you refer to this input’s value to trigger the Flow.

    ![Text Input](/images/powerapps/add-text-input.png)

    ![Text Input](/images/powerapps/add-text-input-2.png)

5. Select Button from the top menu bar.

    - Enter Submit in the Text property on the right-hand menu.
    - Position this button below the text input fields.

    ![Button](/images/powerapps/add-button.png)

6. Select the newly created Submit button.

  - Select OnSelect from the drop-down menu in the upper left-hand corner.

  - In the function bar, enter: FlowName.Run(name.Text, email.Text). This allows us to trigger the Power Automate Flow and pass in the text input fields as parameters that reference the variables initialized in the Flow.

    ![Button Submit](/images/powerapps/button-on-submit.png)

    **WARNING:** Power Apps expects a certain order for the parameters to be passed in and displays an example .Run() function call with the variable names as parameter placeholders. Make sure the order of the parameters match the expected order by Power Apps to prevent invalid argument errors.


    **Note:** Notice that by renaming the text input field elements in Step 23, you can easily reference them when passing them into the .Run() function as parameters. Power Apps has also color-coded each text input field to its corresponding parameter value.


### Step 7: Test Your Automation

Finally, test the entire process end-to-end.

1. Select the Preview button in the upper-right hand corner to fill out the text input fields with recipient information and trigger the Flow by clicking the Submit button.

    ![Preview](/images/powerapps/preview-button.png)

2. Submit a test response clicking on the Submit button.
  ![Preview](/images/powerapps/run-test.png)
3. Verify:
  - Contract is sent to the customer via email.
  - Signature process initiates correctly.
  - Signed contract saves successfully in SharePoint.
  - Business receives an email notification with the signed contract attached.

*Use the following checklist:*

- [ ] Contract sent successfully.
- [ ] Customer receives and signs contract.
- [ ] Signed document stored correctly in SharePoint.
- [ ] Business receives email notification with attachment.

## Troubleshooting & FAQ

### Common Issues:

- **API Key Errors:** Ensure your SignatureAPI key is correct and authenticated.
- **Dynamic Content Mapping:** Double-check placeholder naming in your DOCX file matches exactly with dynamic content mappings.
- **File Access Issues:** Verify permissions and file paths in SharePoint.

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
- [Microsoft Power Apps Documentation](https://www.microsoft.com/power-platform/power-apps-documentation)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)
- [SharePoint Documentation](https://support.microsoft.com/sharepoint)

## Conclusion

By completing this tutorial, you've successfully automated the process of sending, signing, and managing customer agreements. This efficient workflow frees your business from repetitive tasks and ensures customer agreements are signed efficiently and stored automatically.

**Happy Automating!** 