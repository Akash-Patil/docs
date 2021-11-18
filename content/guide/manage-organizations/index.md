---
title: "Manage Organizations"
tags: ["Organization Management", "Manage Organization", "B2B"]
description: "This is a guide for managing organization in the LoginRadius Identity Platform."
path: "/guide/manage-organizations"
---

# Manage Organizations (B2B)

This guide explains how to enable B2B feature, and then manage organizations, their roles, and email settings for the B2B setup.


## Enable B2B Identity

By default **B2B Identity** feature remains disabled for your LoginRadius App. To enable this feature:

1. Log in to your <a href="https://dashboard.loginradius.com/dashboard" target="_blank">LoginRadius Dashboard</a> account. Select your app, then from the left navigation panel, click **B2B Identity**.

2. Click the switch to enable the **B2B Identity** feature.

   ![alt_text](images/enable-feature.png "image_tooltip")

## Manage Organization and Roles

- Manage organizations using these <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity" target="_blank">APIs</a>.

- Add the <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity/#addupdate-roles" target="_blank">default roles</a> for organization users or create new roles using <a href="https://www.loginradius.com/docs/developer/references/api/roles-management/" target="_blank">Role Management APIs</a> and assign them to organization users.

- Add users for the organization using the Add Users <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity/#add-user-to-organization" target="_blank">API</a>.

> Note: Manage Login Methods for your users using the following APIs:
> * <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity/#set-global-idp-on-profile" target="_blank">Set global IDP for the user profile.</a>
> * <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity/#create-an-organizational-saml-idp" target="_blank">Create a SAML login method for the organization.</a>
> * <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity/#set-global-authentication-method-for-organization" target="_blank">Set Global Authentication Method for Organization.</a>
## Manage Email Template and Setting

Manage welcome email template and email settings for the organization as explained below.

### Step 1: Manage Welcome Email

1. **Welcome Email**: The Welcome Email is sent to organization users when they first register on your application. This email contains reset password and login links for the user to reset the account password or to log in to the organization's account.

   ![alt_text](images/welcome-email.png "image_tooltip")

2. You can perform the following actions:

   * Edit template content.
   * Reset the template to its default content.
   * Send a test email using the configured template. You can only send a test email if the required email configuration has been done as explained in <a href="https://www.loginradius.com/docs/developer/guide/setup-your-smtp-provider" target="_blank">SMTP Configuration</a>.

   These actions are available in the action bar highlighted in the image below:

   ![alt_text](images/template-actions.png "image_tooltip")

3. When editing a template, you can update the following:

   * **SUBJECT**: The subject line of the template.
   * **HTML BODY**: The HTML version of the template. This content will appear in clients that support HTML content in emails. You can use [Placeholder](#placeholder-tags) tags in the HTML Body to define where LoginRadius data will appear in your email. 
   * **TEXT BODY**: The plain text version of the template. This content will appear in clients that support only plain text content in emails. You can use the [Placeholder](#placeholder-tags) tags in the Text Body to define where LoginRadius data will appear in your email. 

4. To save your changes, click the **Save** button.

#### Placeholder Tags
These tags are used to define where LoginRadius retrieved data will appear in your email.

   * **#OrgName#**: The organization name provided while creating an organization.
   * **#Name#**: The organization user name provided in your registration form.
   * **#AppName#**: The application name provided while creating a LoginRadius App.
   * **#resetPasswordUrl#**: The URL that is generated by LoginRadius for resetting your user's account password.
   * **#ResetToken#**: The token that is to send along with the reset password URL.
   * **#loginUrl#**: The URL that is sent to your user to login into your organization account.

### Step 2: Manage Email Settings

1. Edit email settings for Welcome Email by clicking the **Email Settings** tab. The following displays the email settings screen:

   ![alt_text](images/email-settings.png "image_tooltip")

2. Configure the following setting:

   **Email Token Validity (Minutes)**: The amount of time (in minutes) of which the reset password link contained in a triggered email will remain valid.

   For example, if the value is set to 10080 (default value), the reset password link in Welcome Email will remain valid for 7 days (10080 minutes).

3. Click the **Save** button to save your changes or **Reset** button to reset configuration value to its default.



## References

* <a href="https://www.loginradius.com/docs/developer/references/api/b2b-identity" target="_blank">B2B Identity APIs</a>
* <a href="https://www.loginradius.com/docs/developer/references/api/roles-management/" target="_blank">Role Management APIs</a>


[Go Back to Home Page](/)