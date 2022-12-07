---
title: "Lab 7: Webex Experience Management"
---

## Overview of the lab:
In this Lab, we will go through the tasks that are required to build a Webex Experience Management connecter and use that to send a survey to customers so that they can provide feedback on their interaction with the contact center. Also, we will look at how the agent can get this feedback on their agent desktop to provide appropriate level of service to end customers. This will enhance their customer satisfaction level which will positively impact their business.


# Table of Contents

- [Part 1: WxM Connector setup](#part-1-wxm-connector-setup----not-need-to-complete-it-already-configured)
  * [1. Identify the API key from WxM](#1-identify-the-api-key-from-wxm)
  * [2. Configure WxM connector in Control hub](#2-configure-wxm-connector-in-control-hub)
- [Part 2: Onboarding CH Agent as WxM User](#part-2-onboarding-ch-agent-as-wxm-user)
  * [1. Create new Agent in CH](#1-create-new-agent-in-ch)
  * [2. Complete the Agent configuration](#2-complete-the-agent-configuration)
- [Part 3: Enable WxM widgets in Desktop Layout](#part-3-enable-wxm-widgets-in-desktop-layout)
  * [1. Enable wxmConfigured flag](#1-enable-wxmconfigured-flag)
  * [2. Enable Customer Experience Journey Widget](#2-enable-customer-experience-journey-widget)
  * [3. Enable Customer Experience Analytics Widget](#3-enable-customer-experience-analytics-widget)
  * [4. Check Widgets in Agent Desktop](#4-check-widgets-in-agent-desktop)
- [Part 4: Configure Feedback node in Flow](#part-4-configure-ivr-feedback-node-in-flow----dont-change-the-logic-of-the-flow)
  * [1. Configure Feedback Node](#1-configure-feedback-node)
  * [2. Test Voice interaction and confirm that voice survey is forwarded to the ANI](#2-test-voice-interaction-and-confirm-that-voice-survey-is-forwarded-to-the-ani)
  * [3.  Validate that the survey filled by the caller is recorded properly in WxM](#3-validate-that-the-survey-filled-by-the-caller-is-recorded-properly-in-wxm)

# Introduction

## Lab Objective
- **Explore the WxM console** and its features

- Edit the **Desktop Layout for configuring the WxM widget**

- Learn how to configure a **flow for geting feedback**
 

## Pre-requisites

1. You have **completed Lab 1 - Control Hub & Admin Portal:**
 - You are familiar with how to create a new user in Control Hub


2. You have **completed Lab 2 - IVR Contact Routing:**
 - You are familiar with creating and modifying flows


3. You have **completed Lab 3 - Agent and Supervisor Desktops:**
 - You are familiar with editing and updating desktop layout JSON


4. You have **Webex Calling installed** in your mobile phone and supervisor created in Lab 1 from which you can make calls to the contact center

> **RECOMMENDED**: To have more information and undestanding about the WxM solution, follow this **[online training](https://salesconnect.cisco.com/#/mylearningmap/SC_LMS_1689  ){:target="_blank"}** first 



## Quick Links
> Control Hub: **[https://admin.webex.com](https://admin.webex.com){:target="_blank"}**\
> Portal: **[https://portal.wxcc-us1.cisco.com/portal](https://portal.wxcc-us1.cisco.com/portal){:target="_blank"}**\
> Agent Desktop: **[https://desktop.wxcc-us1.cisco.com](https://desktop.wxcc-us1.cisco.com){:target="_blank"}**\
> WxM Console: **[https://cx.cloudcherry.com](https://cx.cloudcherry.com){:target="_blank"}**
> WxM Documentation: **[https://xm.webex.com/docs/webex_cc/pcs_ivr_setup](https://xm.webex.com/docs/webex_cc/pcs_ivr_setup){:target="_blank"}**


# Lab Section

> Due to limitation with the number of WxM tenants, all the participants will use the same tenant (**Pod29**) for this lab.

| **Account** | **Username**      | **Password**                   |
| ----------- | ----------------- | -------------------------------- |
| WxM admin       | wxmdemoadmin   | Login@123 |
| Pod29 admin         | admin1pod29@email.carehybrid.com  | WxCC_CiscoLAB123 |


## Part 1: WxM Connector setup -- Not need to complete it, already configured!

* **We have configured the WxM Connector for you**. 

* You just need to login in Control Hub with Pod29 credentials, navigate to _Services -> Contact Center -> Connectors_ and **check that the WxM Connector is already created**.

* However, we recommend you to **have a look to the video and configuration steps below**, to get an idea of how WxM Connector is configured

* For more details, review the **[WxM Documentation](https://xm.webex.com/docs/webex_cc/pcs_ivr_setup){:target="_blank"}**



>The following video outlines the steps required to create the WxM connector. WxCC uses this connector to read the dispatches that are configured in WxM. This connector is also used to load the widgets into the agent desktop and FeedBack is triggered via the same.

<iframe width="1024" height="576" src="https://youtube.com/embed/GI4nzVLLFCk?rel=0" title="WxM Lab" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### 1. Identify the API key from WxM

* Login to the **[WxM console](https://cx.cloudcherry.com){:target="_blank"}**
* Enter the **WxM admin credentials**
* Dismiss informative alerts if any
* In the top-left corner select **_CX Setup_**
* Open the **_Account Settings_** section and navigate to **_Users_**
* In the table, look for the **username with **_`system`_** as a suffix** in the username
> **Note**: Webex XM automatically creates ‘Machine sub-users’ in the account to be used to integrate with any platform
* Copy the username and the **API Key**
* If the API key is not present, click on **Create** and then copy the key.

### 2. Configure WxM connector in Control hub

* Login to [Control Hub](https://admin.webex.com){:target="_blank"}
* Enter the Customer admin email id and the password.
* Navigate to **_Contact Center_** Card
* Click on **_Connectors_**
* Click on **Set Up** on Webex Experience Management card.
* Provide a unique identifier in the Name text area
* Provide the **WxM admin Username**
* Paste the **API Key (copied above)**
* Click on **Save** and then Close.


## Part 2: Onboarding CH Agent as WxM User

### 1. Create new Agent in CH
* Login in the **[Control Hub](https://admin.webex.com){:target="_blank"}** with credentials of **Pod29**
* Create and activate a new agent with the following naming convention: **`WxM_Agent_<ID>_TS@mailinator.com`**
* Make sure you **activate Webex Calling and give an extension number**, like you did in [Lab 1 - Control Hub & Admin Portal](https://wxcctechsummit.github.io/wxcclabguides/TechSummitRoW_2021/Lab1.html){:target="_blank"}

### 2. Complete the Agent configuration
* Navigate to the **[Tenant Portal](https://portal.wxcc-us1.cisco.com/portal){:target="_blank"}** 
* **Create a new Team** for your Agent, name it **``Team1_<ID>_TS``**
* Edit your User and mark **Contact Center Enabled ON**
* Assign the **`MMP_29_TS` Multimedia Profile** to your user
* Add your team to the **`Q_Voice_Test_TS`** Queue distribution group
 
## Part 3: Enable WxM widgets in Desktop Layout

>The following video shows how the Agent Desktop Layout JSON has to be modified with the appropriate values of the WxM dashboard, so that they are loaded into the widgets. The Space ID and the Metrics ID extracted from WxM decide which widget will be loaded for the agent. 
>This lab section assumes that you are familiar with how the agent desktop layout can be modified and applied to a team.

<iframe width="1024" height="576" src="https://youtube.com/embed/Njie8PrB6Kk?rel=0" title="Enabling WxM Widgets" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### 1. Enable wxmConfigured flag

* **Download the current desktop default layout** of the team used by the newly created Agent.
* Edit the JSON file in an editor and search for the key **_wxmConfigured_**.
* If not changed already, change the value to **True**

### 2. Enable Customer Experience Journey Widget

* Login to the **[WxM console](https://cx.cloudcherry.com){:target="_blank"}** with the WxM admin credentials
* Enter the **WxM admin credentials**.
* Dismiss informative alerts if any.
* Navigate to the **Overall Experience** response page.
* Click the Vertical Ellipsis on the right side of the screen and **Export To Cisco Contact Center**
* From the information pane, **copy the Space Id** and update this in the **Customer Experience Journey section** of the layout JSON.
* Since Customer Experience Journey, does not have metricsId **you can use the space Id copied here as the metrics Id**. Else you can copy the metrics Id of Customer Experience Analytics and paste it here (which you will get in the below steps)

### 3. Enable Customer Experience Analytics Widget

* Navigate to the **Agent Dashboard** page.
* Make sure you have **Customer Experience Analytics** selected under **Metrics** tab top-left corner
* Click the Vertical Ellipsis on the right side of the screen and **Export To Cisco Contact Center**
* From the information pane, copy the **Space Id** and update this in the **Customer Experience Analytics section** of the layout JSON.
* From the information pane, copy the **Metrics Id** and update this in the **Customer Experience Analytics section** of the layout JSON.
* Save this JSON file and **upload** it in the appropriate layout used by the Test Agents Team.
* Now if you login as an agent and test Voice interactions, you will be able to access both the Customer Experience Analytics widget and Customer Experience Journey widget.

### 4. Check Widgets in Agent Desktop

* Login in the **[Agent Desktop](https://desktop.wxcc-us1.cisco.com){:target="_blank"}** with the recently created agent credentials
* Confirm that the **WxM widgets are visible on Agent Desktop.**


## Part 4: Configure Feedback node in Flow -- Don't change the logic of the flow!

* **We have created the Flow and configured the Feedback node for you.**


* Login in the Tenant Portal, navigate to Routing Strategies and open the **`WxM_Feedback_Flow`** to see how the Flow Diagram is buit.
**DO NOT change the logic of the flow**, since the pod is shared with all partners


* You can also **see the video and the Configure Feedback Node section** to know the configuration steps needed.

* **THE VIDEO EXPLAINS HOW TO CONFIGURE EMAIL/SMS FEEFBACK. For IVR Feedback configuration, follow the steps written below.**

>The video does a quick demo on how the Feedback node should be implemented such that the caller receives an email with the survey link after completion of the call. The same steps can be followed to trigger an SMS with the survey link to the call ANI. 

<iframe width="1024" height="576" src="https://youtube.com/embed/qGW6lRI7AA0?rel=0" title="Configure Feedback Node" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### 1. Configure IVR Feedback Node

* Login to portal with admin credential and navigate to **`Routing Strategy`**
* Click on **`Flows` and open the WxM_Feedback_Flow**
* Create **2 custom variables** and hardcode values that will be used as **Customer ID and Email**.
* Under Main flow, ensure that a **`Global_FeedbackSurveyOptin`** variable is given **`TRUE`** value either by menu choice or prompt for customer to opt-in.
* Navigate to **`Event Flows` and Drag the Feedback V2** note from the library on the left and **connect it the AgentDisconnected event**
* Within the Feedback V2 settings on the right-hand side, select the **Survey Method as Voice based, and choose the survey named “Contact Center IVR Feedback”**
* Under the Set Language button, **select English (US)** from the drop-down.
* From the drop-down select appropriate values for **`Customer ID` (custom variable), `Email` (custom variable) and `Phone Number` (NewPhoneContact.ANI)**
* **Validate the flow and publish it.**

### 2. Test Voice interaction and confirm that voice survey is forwarded to the ANI

* Login in the **[Agent Desktop](https://desktop.wxcc-us1.cisco.com){:target="_blank"}** with the recently created agent credentials
* **Trigger a voice call** from the Webex Calling app to the Dial Number mapped to the Entry Point: **`+14402308308`**
* End the call **from the Agent Desktop and select a Wrap-Up option**
* A **voice surver should be played** in the caller user phone
* Complete the voice survey **dialing a number** in the Webex Calling app from where you made the call
* After testing the call interaction **move the Agent to non-Available status**, so the rest of attendees can also complete this task

### 3. Validate that the survey filled by the caller is recorded properly in WxM

* Login to the **[WxM console](https://cx.cloudcherry.com){:target="_blank"}**
* Enter the **WxM admin credentials.**
* Navigate to the **`Overall Experience`** dashboard and ensure that the feedback you provided is recorded

---

### Congratulations, you have compleated Lab 7 tasks! 
### We would like to keep track of your progress and make sure that we are giving you effective support. Please take approximately one minute to complete the short survey.


<script>
function mainPage() {window.location.href = "https://wxcctechsummit.github.io/wxcclabguides/TechSummitRoW_2021/HomePage.html";}
function nextLab() 
 {
 window.open("https://app.smartsheet.com/b/form/42c2c1f4e71940088ad0ea8053ac3006", '_blank');
 window.location.href = "https://wxcctechsummit.github.io/wxcclabguides/TechSummitRoW_2021/APIs.html";
 }
</script>

<div id="button-row">
	<button onclick="mainPage()" style="
  border-radius: 5px;
  background-color: rgb(116,191,75);
  padding: 10px;">Go back to Main Page</button>

<button onclick="nextLab()" style="
  position: absolute;
  right: 200px;
  border-radius: 5px;
  background-color: rgb(116,191,75);
  padding: 10px;">Take Survey and Go to Lab 8</button>

</div>
