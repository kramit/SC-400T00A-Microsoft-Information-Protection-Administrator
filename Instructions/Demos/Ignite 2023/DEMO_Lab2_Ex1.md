---
lab:
    title: 'Exercise 1 - Manage DLP Policies'
    module: 'Module 2 - Implement Data Loss Prevention'
---
# Exercise 4 - Manage DLP Policies

You are Joni Sherman, the newly hired Compliance Administrator for Contoso Ltd. tasked to configure the company's Microsoft 365 tenant for data loss prevention. Contoso Ltd. is a company that offers driving instruction in the United States, and you need to make sure that sensitive customer information does not leave the organization.

## Task 1 – Create a DLP policy in test mode

In this exercise, you will create a Data Loss Prevention policy in the Microsoft Purview portal to protect sensitive data from being shared by users. The DLP Policy that you create will inform your users if they want to share content that contains Credit Card information and allow them to provide a justification for sending this information. The policy will be implemented in test mode because you do not want the block action to affect your users yet.

1. Log into Client 1 VM (LON-CL1) as the **lon-cl1\admin** account.

1. In **Microsoft Edge**, navigate to **https://compliance.microsoft.com** and log into the Microsoft Purview portal as **Joni Sherman**. Joni's password should be provided by your lab hosting provider.

1. If the **Stay signed in?** dialog box appears, select the **Don’t show this again** checkbox and then select **No**.

1. In the **Microsoft Purview** portal, in the left navigation pane, expand **Data loss prevention** then select **Policies**.

1. On the **Policies** page select **+ Create policy** to start the wizard for creating a new data loss prevention policy.

1. On the **Start with a template or create a custom policy** page, scroll down and select **Custom** under **Categories** and **Custom policy** under **Templates**. By default, both options should already be selected , select **Next**.

1. On the **Name your DLP policy** page enter:

   - **Name**: Credit Card DLP Policy
   - **Description**: Protect credit card numbers from being shared

1. Select **Next**.

1. On the **Assign admin units** page select **Next**.

1. On the **Choose locations to apply the policy** page, enable the **Teams chat and channel messages** option only and select **Next**.

1. On the **Define policy settings** page, select **Create or customize advanced DLP rules** and select **Next**.

1. On the **Customize advanced DLP rules** page, select **+ Create rule**.

1. On the **Create rule** flyout page, type _Credit card information_ in the **Name** field.

1. Under **Conditions**, select **+ Add Condition** and then select **Content contains** from the dropdown menu.

1. In the new **Content contains** area, select **Add** and select **Sensitive info types** from the dropdown menu.

1. On the **Sensitive info types** page, select **Credit Card Number** and select **Add**.

1. On the **Create rule** page, select **+ Add condition** and select **Content is shared from Microsoft 365** from the dropdown menu.

1. In the new **Content is shared from Microsoft 365** section, select the **only with people inside my organization** option.

1. On the **Create rule** page, select **+ Add an action**. Under **Use actions to protect content when the conditions are met.** expand **Restrict access or encrypt the content in Microsoft 365 locations** and select **Block everyone.**

1. On the **Create rule** page, in the **User notifications** section, select the switch to **On** to enable user notifications.

1. Select the check box to enable **Notify users in Office 365 service with a policy tip**.

1. Under **User overrides** select the checkbox to **Allow overrides from M365 services. Allows users in Exchange, SharePoint, OneDrive and Teams to override policy restrictions.**

1. Check the checkbox to **Require a business justification to override**.

1. In the **Incident reports** section, in the **Use this severity level in admin alerts and reports** dropdown, select **Low**.

1. On the **Create rule** page select **Save** then select **Next**.

1. On the **Policy mode** page select **Run the policy in test mode** and select **Show policy tips while in simulation mode**, then select **Next**.

1. On the **Review your policy and create it** page review your settings then select **Submit**

1. On the **New policy created** page select **Done**.

You have now created a DLP policy that scans Credit Card numbers in Microsoft Teams chats and channels and allows users to provide a business justification to override the policy.

## Task 2 - Modify a DLP policy

In this task, you will modify the existing DLP policy you created in the previous step to also scan e-mails for Credit Card information and inform users if they want to share this content in an e-mail.

1. You should still be logged into Client 1 VM (LON-CL1) as the **lon-cl1\admin** account, and you should be logged into Microsoft 365 as **Joni Sherman**.

1. In **Microsoft Edge**, the Microsoft Purview portal tab should still be open. If so, select it and proceed to the next step. If you closed it, then in a new tab, navigate to **https://compliance.microsoft.com**.

1. In the **Microsoft Purview** portal, in the left navigation pane, expand **Data loss prevention** then select **Policies**.

1. On the **Policies** page select the checkbox next to the recently created **Credit Card DLP Policy**, then select **Edit policy** to start the policy wizard.

1. On the **Name your DLP policy** page, select **Next**.

1. On the **Assign admin units** page select **Next**.

1. On the **Choose locations to apply the policy** page, enable the **Exchange email** option and then select **Next** until you reach the **Review your policy and create it** page.

1. Select **Submit** to apply the change you made to the policy.

1. Once the policy is updated select **Done**.

You have now modified an existing DLP policy and changed the locations it scans for content.

## Task 3 - Create a DLP policy in PowerShell

In this task, you use PowerShell to create a DLP policy to protect the Contoso EmployeeIDs and prevent them from being shared in Exchange. Users will be informed that they are attempting to share sensitive data and are blocked from sending the e-mail if it includes Contoso EmployeeIDs.

1. You should still be logged into Client 1 VM (LON-CL1) as the **lon-cl1\admin** account.

1. In the start menu, select **Windows PowerShell**.

1. Enter the following cmdlet to install the latest Exchange Online PowerShell module version:

    ```powershell
    Install-Module ExchangeOnlineManagement
    ```

1. Confirm the NuGet provider security dialog with **Y** for Yes and press **Enter**. This process may take some time to complete.

1. Confirm the Untrusted repository security dialog with **Y** for Yes and press **Enter**. This process may take some time to complete.

1. Enter the following cmdlet to change your execution policy and press **Enter**

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

1. In the **PowerShell** window, enter

    ```powershell
    Connect-IPPSSession
    ```

   then sign in as **Joni Sherman** JoniS@WWLxZZZZZZ.onmicrosoft.com. Joni's password should be provided by your lab hosting provider.

1. Enter the following command into PowerShell to create a DLP policy that scans all Exchange mailboxes:

    ```powershell
    New-DlpCompliancePolicy -Name "EmployeeID DLP Policy" -Comment "This policy blocks sharing of Employee IDs" -ExchangeLocation All
    ```

1. Enter the following command into PowerShell to add a DLP rule to the DLP policy you created in the previous step:

    ```powershell
    New-DlpComplianceRule -Name "EmployeeID DLP rule" -Policy "EmployeeID DLP Policy" -BlockAccess $true -ContentContainsSensitiveInformation @{Name="Contoso Employee IDs"}
    ```

1. Use the following command to review the **EmployeeID DLP rule**:

    ```powershell
    Get-DLPComplianceRule -Identity "EmployeeID DLP rule"
    ```

You have now created a DLP Policy that scans Contoso EmployeeIDs in Exchange by using PowerShell.

## Task 4 - Test your DLP Policy

In this task you'll test the DLP policy that was created in the previous task.

1. You should still be logged into Client 1 VM (LON-CL1) as the **lon-cl1\admin** account and logged into Microsoft 365 as Joni Sherman.

1. Open a Microsoft Edge browser window and navigate to **https://outlook.office.com/**

1. Select the **New mail** button on the top left to compose a new email message.

1. In the **To** field, enter _Megan_ and select **Megan Bowen**'s email address.

1. In the subject field enter _Help with employee information_.

1. In the body of the email enter:

    ``` text
    Please help me with the start dates for the following employees:
    ABC123456
    DEF678901
    GHI234567

    Thank you, 
    Joni Sherman
    ```

1. Select the **Send** button in the upper right of the message window to send the email.

1. You should receive a message that the email was undeliverable and blocked by a DLP policy.

      ![Screenshot of Manage roles option](../Media/dlp-email-blocked.png)

You have successfully tested your DLP policy.

## Task 5 - Activate a policy in test mode

In this task, you will activate the credit card information DLP policy you created in test mode so it enforces its protective actions.

1. You should still be logged into Client 1 VM (LON-CL1) as the **lon-cl1\admin** account, and you should be logged into Microsoft 365 as **Joni Sherman**.

1. In **Microsoft Edge**, the Microsoft Purview portal tab should still be open. If so, select it and proceed to the next step. If you closed it, then in a new tab, navigate to **https://compliance.microsoft.com**.

1. In the **Microsoft Purview** portal, in the left navigation pane, expand **Data loss prevention** then select **Policies**.

1. On the **Policies** page select the checkbox next to **Credit Card DLP Policy** then select **Edit policy** to start the policy wizard.

1. Select **Next** until you reach the **Policy mode** page and select **Turn it on right away**.

1. On the **Review your policy and create it** select **Submit**.

1. On the **Policy updated** page select **Done**.

You have successfully activated the DLP Policy. If the policy detects an attempt to share credit card information, it will now block the attempt and allow the users to provide a business justification to override the block action.

## Task 6 - Modify policy priority

After creating two DLP policies, you want to make sure that the more restrictive policy is processed at a higher priority than the less restrictive policy. For this reason, you want to move the EmployeeID DLP Policy into the higher priority.

1. You should still be logged into Client 1 VM (LON-CL1) as the **lon-cl1\admin** account, and you should be logged into Microsoft 365 as **Joni Sherman**.

1. In **Microsoft Edge**, the Microsoft Purview portal tab should still be open. If so, select it and proceed to the next step. If you closed it, then in a new tab, navigate to **https://compliance.microsoft.com**.

1. In the **Microsoft Purview** portal, in the left navigation pane, expand **Data loss prevention** then select **Policies**.

1. On the **Policies** select the three vertical dots next to the **EmployeeID DLP Policy** to open the **Actions** selection and select **Move to top**.

1. In the **Data loss prevention** window, select **Refresh** and review the priority in the **Order** column of the policy table.

1. Select the circle with Joni's profile image in the upper right and select **Sign out** to sign out of Joni's account for the next exercise.

You successfully modified the priority of your DLP policies. If both policies match the same content the action of the higher priority policy will be enforced.
