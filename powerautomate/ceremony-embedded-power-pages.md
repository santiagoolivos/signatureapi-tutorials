---
title: Embed a Signature Ceremony in a Power Pages with SignatureAPI and Microsoft Power Automate
---

## Overview

This tutorial demonstrates how to embed a signature ceremony in a Power Pages with SignatureAPI and Microsoft Power Automate. By integrating **Power Pages** (for having a form and embedding the signature ceremony), **OneDrive** (for storing the template), and the **SignatureAPI connector** (for electronic signatures), you can automate the signature process from the Power Pages form.

### What You’ll Learn

* How to trigger a flow with a new Power Pages form submission.
* Retrieving and pre-filling a DOCX contract template from OneDrive.
* Creating and sending a signature envelope using SignatureAPI.
* Editing the Power Pages code to run the power automate flow from the submission of a form.
* Embedding the signature ceremony in a Power Pages form.

### The Problem

Many companies need to collect information from their customers and sign documents. But sometimes the process is not automated and the customer needs to sign the document manually. Also many companies wants  to have a more personalized signature process showed in its website, so they can have a better user experience.

* **Manual signature process** – the customer needs to sign the document manually.
* **Not personalized** – the signature process is not personalized and the customer needs to sign the document in a different page than the one where the information is collected.


### How Automation Helps

Automation simplifies this process by:

* Automatically creating an embedding the signature ceremony in the Power Pages form.

## Requirements

Before starting, make sure you have:

* **Power Automate** – To build workflows.
* **SignatureAPI account** – For electronic signatures.
* **Power Pages** – For having a form and embedding the signature ceremony.
* **OneDrive** – For storing your DOCX templates.

## Flow Overview

The automation process follows these steps:

1. **Trigger:** Power Pages form submission starts the flow.
2. **Data Retrieval:** Get customer details and fetch the template from OneDrive.
3. **Signature Process:** Create an envelope via SignatureAPI, add recipient details, and attach the DOCX.
4. **Create a Ceremony:** Create and return the ceremony to be embedded in the Power Pages form.

Here’s what your final Power Automate flow will look like:

![Flow](/images/powerpages/complete-flow.png)

## Step-by-Step Tutorial

Follow these steps to automate your signature process using Power Pages, SignatureAPI, and Power Automate:

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

5. Save your template and upload it to **Documents** in the **Sharepoint Site**.

**Important:**
- Ensure placeholder keys match exactly with what you'll use later in Power Automate.
- Keep your template simple and clear to avoid confusion during dynamic insertion.

![Word Template Example](/images/word/customer-agreement.png)



### Step 2: Create the Power Pages


1. Visit [Power Pages](https://make.powerpages.microsoft.com) and sign in.
2. From the left-hand menu bar, in the **"Home"** section, click on **"Start from blank"**.

    ![New Power Pages](/images/powerpages/start-from-blank.png)

3. Name the site (e.g Customer Agreement Form) and set the web address (e.g customer-agreement-form) and click on **"Done"**.

    ![New Power Pages](/images/powerpages/name-address.png)

### Step 3: Set Up the Power Automate Flow

Now, create the automated workflow in Power Automate, directly from Power Pages.


#### 3.1 Set flow trigger

First, set the flow trigger to run whenever power pages calls a flow.

1. From the left-hand menu bar, click on **"Set up"** and select **"Cloud Flows"**.

2. Here you can see the flow you created earlier for any power pages. But in this case we will create a new flow. Click on **"Create new flow"**.

    ![Trigger](/images/powerpages/create-new-flow.png)

3. Rename the flow (e.g Customer Agreement Flow) and select the power pages trigger **"When Power Pages calls a flow"** and name the flow (for example, Customer Agreement).

    ![Trigger](/images/powerpages/select-trigger.png)

#### 3.2 Add inputs

1. Click on the Trigger and select **"Add an input"**. Then select the **"Text"** type and enter a name for the input as "customerName". Do it also for the other variable as "customerEmail".

    ![Add Input 1](/images/powerpages/add-input-1.png)

    ![Add Input 2](/images/powerpages/add-input-2.png)


### Step 4: Retrieve Contract Template from OneDrive


Now, fetch your customer agreement template stored in OneDrive.

1. Add **"Get File Content using Path"** from the OneDrive connector.
2. Select the DOCX template stored in your OneDrive.

    ![Get File Content](/images/powerpages/get-file-content.png)

### Step 5: Set Up the Signature Process

In this step, you'll configure SignatureAPI to create, send, and track the signature process.

#### 5.1 Create a SignatureAPI Envelope

Begin by creating an envelope to hold your contract and signature process.

1. Add the **"Create an Envelope"** action (SignatureAPI connector).
2. If prompted, authenticate your connection using your SignatureAPI key from the [SignatureAPI Dashboard](https://dashboard.signatureapi.com/settings/api-keys).

    ![Create Connection](/images/powerpages/create-connection.png)

3. Set an **Envelope Title** (e.g., customer name) and email message using dynamic content.

    ![Create Envelope](/images/powerpages/create-envelope.png)

#### 5.2 Add the Recipient - Signer

Next, specify who will receive and sign the contract which manual ceremony creation.

1. Add **"Add Recipient - Signer"** action.
2. Set the **Envelope ID** from the dynamic content.
3. Map **Recipient Name** and **Recipient Email** using form details (Dynamic Content).
4. Set the **Recipient Key** (e.g., "customer"), matching your DOCX placeholders.
5. From the **advanced options** select in the **"Recipient Ceremony Creation"** the value **manual**.

    ![Add Recipient](/images/powerpages/add-recipient.png)

#### 5.3 Attach the DOCX Contract Template

Now, attach your contract template to the envelope and populate it with customer details.

1. Add **"Add a Template – DOCX"** action.
2. Set the **Envelope ID** from the dynamic content.
3. Select **File Content** from the Sharepoint action.
4. Set the **Document Title** (e.g., "Customer Agreement").
5. Ensure your DOCX template uses placeholders (`{{customer.name}}`, etc.) and map each field to the corresponding dynamic content from the Power Apps initialized variables.

    ![Add Template](/images/powerpages/add-template.png)

#### 5.4 Define Signature Placement

Specify where the customer should sign on the document.

1. Add **"Add a Place – Signature"** action.
2. Set the **Document ID** using the dynamic content.
3. Use the placeholder (e.g., `[[customer_signature]]`) from your DOCX template.
4. Set the **Recipient Key** using the dynamic content.

    ![Add Place](/images/powerpages/add-signature.png)

#### 5.5 Start the Signing Process

Trigger the sending of your envelope to the customer for signing.

1. Add **"Start Envelope"** action.
2. Set the **Envelope ID** using the dynamic content.

    ![Start Envelope](/images/powerpages/start-envelope.png)


#### 5.6 Create the Ceremony for the customer

Create the ceremony for the customer with custom authentication. This will return an URL that will be used to embed the ceremony in the Power Pages.

1. Add **"Create Ceremony - Custom authentication"** action from the SignatureAPI connector.
2. Set the **"Recipient ID"** using dynamic content.
3. Set the **"Authentication Provider"** to what you want to use. This field will be used in the Audit Log of the Deliverable as the security provider for that custom ceremony.
4. Set the **"Authentication Data"** to the data you want to use to verify the user and authentication provider. This will be used in the Audit Log as a security provider data.
    
5. from the advanced options, in the **"Extra Properties"** section, add the following:

    ```json
    {"embeddable_in":["*"]}
    ```
    This will allow the ceremony to be embedded in any Power Pages.

![Extra Properties](/images/powerpages/create-ceremony.png)

#### 5.7 Return the created ceremony URL to the Power Pages

1. Add an output of type **"Text"**.
2. Set the **"Name"** to the name of the variable you want to return (e.g. url).
3. Set the **"Value"** to the URL of the ceremony from the **Create Ceremony - Custom authentication** action using dynamic content.

![Return Ceremony](/images/powerpages/return-ceremony.png)


### Step 6: Create the flow and add it to Power Pages


1. Save the flow and copy the flow URL.
2. Set the roles and permissions for the flow. In this case allow all the roles to access the flow.

![Flow URL](/images/powerpages/add-flow.png)


### Step 7: Power Pages Form and Embedding Ceremony

Now, you need to add the ceremony to the Power Pages form. Consider that  to run the power automate flow from the submission of a form, its necessary to add some code to the page. So you will need to do that from the vscode editor with the power page code.

#### 7.1 Open power pages with the vscode editor

1. From the left-hand menu bar, click on **"Pages"**. Here you can see all the pages of the site. You can add many pages as you want and all the things you need using the power pages editor. But to run the power automate flow one the submission of a form  its necessary to add some code to the page. 

    ![Edit Code](/images/powerpages/show-pages.png)


2. Select Edit code. This will open the vscode editor with the power page code, opening the home page (what you were seeing before you clicked on edit code).

    ![Edit Code](/images/powerpages/vscode-opened.png)


#### 7.2 Add the ceremony to the Power Pages form

Now in the vscode editor, specifically in the Home.html file you will need to add the following code. It is adding a form to run the power automate flow one the submission of a form.  You can add the entire code and then replace the _url with the flow URL (from the Step 5) or you can add the code step by step.

```html
<div class="row sectionBlockLayout text-start" style="display: flex; flex-wrap: wrap; margin: 0; min-height: auto; padding: 8px; justify-content: center;">
  <div class="container" style="padding: 0; display: flex; flex-wrap: wrap; max-width: 600px;">
    <div class="col-lg-12 columnBlockLayout" style="flex-grow: 1; display: flex; flex-direction: column; min-width: 250px; align-items: center;">
      
      <!-- Title -->
      <h2 style="text-align: center; margin-bottom: 16px;">
        Customer Agreement Form
      </h2>

      <!-- Description -->
      <p style="text-align: center; margin-bottom: 24px;">
        Please fill out the form below. We'll use this information to generate or request an customer agreement.
      </p>

      <!-- Form -->
      <form id="contractForm" style="width: 100%; max-width: 400px; display: flex; flex-direction: column; gap: 12px;">
        
        <!-- Name Field -->
        <label for="name" style="font-weight: bold;">Name</label>
        <input
          type="text"
          id="name"
          placeholder="Enter your full name"
          required
          style="padding: 10px; border: 1px solid #ccc; border-radius: 4px;"
        />

        <!-- Email Field -->
        <label for="email" style="font-weight: bold;">Email</label>
        <input
          type="email"
          id="email"
          placeholder="Enter your email address"
          required
          style="padding: 10px; border: 1px solid #ccc; border-radius: 4px;"
        />

        <!-- Submit Button -->
        <button
          type="submit"
          style="
            background-color: #007bff;
            color: #fff;
            padding: 12px;
            border: none;
            border-radius: 4px;
            margin-top: 8px;
            cursor: pointer;
            font-size: 16px;
          "
        >
          Send Request
        </button>
      </form>
    </div>
  </div>
</div>


<div class="row sectionBlockLayout text-start" style="display: flex; flex-wrap: wrap; margin: 0px; min-height: auto; padding: 8px;">
  <div class="container" style="padding: 0px; display: flex; flex-wrap: wrap;"><div class="col-lg-12 columnBlockLayout" style="flex-grow: 1; display: flex; flex-direction: column; min-width: 250px;">

    <iframe frameborder="0" id="iframeToShow" src="https://www.bing.com" style="display: none; width: 100%; height: 500px;"></iframe>
  </div></div>
</div>

<script>
  document.addEventListener("DOMContentLoaded", function () {
    document.getElementById("contractForm").addEventListener("submit", function (e) {
      e.preventDefault();

      const name = document.getElementById("name").value;
      const email = document.getElementById("email").value;

      const _url = "Replace with the flow URL";

      var data = {}

      data["customerName"] = name
      data["customerEmail"] = email

      var payload = {}
      payload.eventData = JSON.stringify(data)


      console.log("payload", payload);

      shell
      .ajaxSafePost({
        type: "POST",
        contentType: "application/json",
        url: _url,
        data: JSON.stringify(payload),
        processData: false,
        global: false,
      })
      .done(function (response) {
        console.log("Response:", response);
        const result = JSON.parse(response);

        const iframe = document.getElementById("iframeToShow");
        iframe.src = result.url + "&embedded=true&event_delivery=message";
        iframe.style.display = "block";
      })
      .fail(function (error) {
        console.log("error: ", error);
        alert("Something went wrong.");
      });
    });
  });
</script>
```

1. Copy and paste the code in the vscode editor.

2. Replace the _url with the flow URL (from the Step 5)

    ![Add Ceremony](/images/powerpages/vscode-edit-url.png)

    **Important:**
    Note that inside the script the following code is used to define the inputs to the power automate flow:

    ```javascript
    var data = {}

    data["customerName"] = name
    data["customerEmail"] = email
    ```

    This is the data that will be sent to the power automate flow, and it has to be named as the inputs defined in the flow (from the Step 3.2.1). If you change the name or add more inputs in the Step 3.2.1, you will need to change the name or add the new variables in the script.

3. Save the file and refresh the power pages page.

    This code have the form and when clicking on the submit button it will run the power automate flow and embed the ceremony in the power pages. Consider that it has some styles and a minimum logic and user friendly interface. You can improve it as you want.

    ![Add Ceremony](/images/powerpages/vscode-with-form.png)


### Step 8: Test Your Automation

Finally, test the entire process end-to-end.

1. Go back to power pages. You can see the form you created in the home page. Click on the preview button and select the Desktop option.

    ![Add Ceremony](/images/powerpages/click-preview.png)

2. Fill the form and click on the submit button. You will have to wait a few seconds and you will see the ceremony embedded in the page.

    ![Add Ceremony](/images/powerpages/preview-form.png)

    ![Add Ceremony](/images/powerpages/preview-embedded.png)


3. Verify:
  - Ceremony is embedded in the page.
  - Ceremony works correctly and can be signed.

*Use the following checklist:*

- [ ] Ceremony is embedded in the page.
- [ ] Ceremony works correctly and can be signed.

## Troubleshooting & FAQ

### Common Issues:

- **API Key Errors:** Ensure your SignatureAPI key is correct and authenticated.
- **Dynamic Content Mapping:** Double-check placeholder naming in your DOCX file matches exactly with dynamic content mappings.
- **File Access Issues:** Verify permissions and file paths in OneDrive.


### Frequently Asked Questions:

- *What if the ceremony isn't showed?*  
  Check the browser logs, it will show you the error. Also check if the flow is running correctly.
- *Can I adapt this for other document types?*  
  Yes, this method is adaptable for any automated document-signing workflow.
- *Can I use this for other purposes?*  
  Yes, this method is adaptable for any automated document-signing workflow.

## Best Practices & Security

- Always securely manage API keys.
- Regularly check flow runs in Power Automate for any errors.
- Document any flow or template changes for future reference.

## Additional Resources

- [SignatureAPI Documentation](https://signatureapi.com/docs)
- [Power Pages Documentation](https://powerpages.microsoft.com)
- [Power Automate Community](https://powerusers.microsoft.com/t5/Microsoft-Power-Automate/ct-p/MPACommunity)

## Conclusion

By completing this tutorial, you have learned how to embed a ceremony in a Power Pages with SignatureAPI and Microsoft Power Automate.

**Happy automating!**