# 📘 **Company IT — New Hire Automation (Google Apps Script Suite)**

This repository contains a set of four Google Apps Script automations used to streamline new-hire onboarding, covering everything from user provisioning, Jira ticket creation, Slack notifications, and password-reset workflows.

These scripts were originally built to help IT automate manual onboarding processes while integrating with **Google Workspace**, **Slack**, **Jira**, and **Google Sheets**.

---

# 🚀 **Overview**

This repo contains four separate Apps Script projects:

1. **Jira Ticket Creation**
2. **Google Admin SDK – User Creation Automation**
3. **Slack Notification Bot**
4. **Friday → Monday Password Reset Automation**

Each script reads from **a shared Google Sheet** where the New Hire data lives.
Each script has its own **trigger**, **API configuration**, and **purpose**, and must be deployed separately inside Apps Script.

A template for the Google Sheet is included in the **Jira Ticket Creation script**, defining all required columns.

---

# 📄 **Google Sheet Requirements**

All scripts rely on a single, shared Google Sheet where new hire information is maintained.

The columns required by the automation are listed in the `Jira Ticket Creation` script, and a template is provided in this repository (or referenced in the documentation).

### **Required Columns (By Script)**

#### ✔ **Common Columns**

| Column                | Purpose                               |
| --------------------- | ------------------------------------- |
| Name                  | New Hire Full Name                    |
| Title                 | Job Title                             |
| Division              | Org division (Engineering, G&A, etc.) |
| Department            | Team name                             |
| Manager               | Manager name or email                 |
| Employment Type       | Full-Time / Contractor / Part-Time    |
| Start Date            | Hire date used for triggers           |
| Orientation           | Orientation date                      |
| Employment Location   | Toronto, Remote, Texas, etc.          |
| Desk Assignment       | Optional                              |
| Personal Email        | Used to verify candidate identity     |
| Address               | Optional                              |
| Laptop Type           | MacBook / Dell / Lenovo               |
| Jira Ticket Status    | Updated by Script #1                  |
| Onboarding Status     | Used for Script #2 & #4               |
| Created Email         | Written by Script #2                  |
| Password Reset Status | Written by Script #4                  |

The sheet can contain unused columns — what matters is that **the required ones match** the names used in the scripts.

---

# 📁 **Repository Contents**

```
/jira-ticket-creation.gs
/admin-user-creation.gs
/slack-announcement.gs
/password-reset-automation.gs
/README.md   ← (this file)
```

Each script is self-contained and includes a **Configuration** section at the top where you must update your environment-specific values.

---

# 🔧 **Configuration Requirements (All Scripts)**

Every script requires you to update a few key variables before running it.
These are grouped at the top of each `.gs` file under a clearly labeled:

```
/***********************
 *   CONFIGURATION
 ***********************/
```

You **must** update the following fields:

### ✔ **1. Google Sheet References**

* `SPREADSHEET_ID`
* `SHEET_NAME`

### ✔ **2. Jira (Script #1)**

* `JIRA_BASE_URL`
* `JIRA_EMAIL`
* `JIRA_API_TOKEN`
* `JIRA_PROJECT_KEY`
* `JIRA_ISSUE_TYPE`
* `ASSIGNEE_EMAIL` (optional)

### ✔ **3. Google Admin SDK (Script #2)**

* Service account with **domain-wide delegation**
* Admin SDK Directory API enabled in Google Cloud
* Scopes added:

  ```
  https://www.googleapis.com/auth/admin.directory.user
  https://www.googleapis.com/auth/admin.directory.group
  ```
* `SERVICE_ACCOUNT_EMAIL`
* `PRIVATE_KEY`
* Group mappings (Engineering, IT, Finance, etc.)
* Field mappings for user attributes (division, department, etc.)

### ✔ **4. Slack Bot (Script #3)**

* `SLACK_WEBHOOK_URL`
* Channel formatting (optional)
* Block/Markdown payload (optional)

### ✔ **5. Password Reset Script (Script #4)**

* Admin SDK configuration (same as Script #2)
* Reset window (usually scans for hires starting Monday)
* Status column names (`Onboarding Status`, `Password Reset Status`)

---

# 📜 **Script-by-Script Overview**

---

## 🟦 **1. Jira Ticket Creation Script**

### **Purpose**

Automatically creates a Jira ticket for every new hire so IT can track onboarding tasks.

### **What It Does**

* Reads new hire details from the sheet
* Generates a properly formatted Jira ticket
* Assigns to IT or a specific assignee
* Writes the Jira ticket status back to the sheet

### **Trigger**

* **On edit** (recommended)
  OR
* Button-driven custom menu

### **Configuration Needed**

* Jira API token
* Jira project key
* Jira base URL
* Sheet ID + Name
* Column mappings (already included in template)

---

## 🟩 **2. Google Admin SDK — User Creation Script**

### **Purpose**

Creates Google Workspace user accounts automatically.

### **What It Does**

* Creates new user accounts (first name, last name, password)
* Sets user attributes (Division, Department, Manager, Location)
* Adds user to groups based on department/division rules
* Writes the newly created email back to the Sheet
* Updates new hire “Onboarding Status”

### **Trigger**

* **On edit** (row marked as “Processed”)
  OR
* Manual menu action

### **Configuration Needed**

* Service account email
* Private key
* Domain delegation
* Admin SDK API scopes
* Group mapping rules

---

## 🟨 **3. Slack Notification Bot**

### **Purpose**

Alerts Talent, IT, or management when certain onboarding actions occur.

### **What It Does**

* Sends structured Slack messages for:

  * New accounts created
  * Start dates
  * Status updates
* Includes custom formatting

### **Trigger**

* **After user creation script runs**
* Or cron-based trigger (optional)

### **Configuration Needed**

* Slack webhook URL
* Sheet ID + relevant columns
* Payload formatting

---

## 🟧 **4. Password Reset Automation (Friday → Monday Cycle)**

### **Purpose**

Ensures that new hires starting on Monday receive a fresh, valid password.

### **What It Does**

* Looks at next week’s start dates
* Issues password resets through Admin SDK
* Emails out new temporary passwords
* Updates the “Password Reset Status” field
* Supports re-sending (e.g., “Sent x2”)

### **Trigger**

* **Time-driven trigger: Friday at 4pm**
  (or any schedule you define)

### **Configuration Needed**

* Admin SDK access
* Sheet ID + column mapping
* Email template
* Logic for how far ahead to check (default: 3 days)

---

# 🧩 **Why Four Separate Scripts?**

Google Apps Script limits triggers:

* A single script cannot reliably manage multiple different triggers
* Each workflow has its own logic, schedule, and dependencies
* Separation avoids execution conflicts and keeps logs cleaner

Thus, each script is packaged individually but shares a common Sheet.

---

# 🛠 **How to Deploy**

1. Copy each `.gs` file into its own Apps Script project
2. Update configuration sections at the top
3. Deploy or run once manually
4. Create triggers:

   * Jira: on edit
   * User creation: on edit/manual
   * Slack: optional
   * Password reset: time-based (Friday 4:00 PM)

---

# 🧪 **Testing**

Before using real data:

1. Make a **copy of the template Google Sheet**
2. Populate it with **dummy data**
3. Test each script separately:

   * Create user → verify in admin console
   * Create Jira ticket → check Jira
   * Slack message → check channel
   * Password reset → inspect execution log
4. Confirm that status fields update properly

Testing is safe—scripts fail gracefully and write logs to the Apps Script execution console.

---

# 🔮 **Future Enhancements**

Planned improvements:

* Integrating with **Lever API** to pull candidates automatically
* BambooHR → Workspace provisioning
* SCIM sync to downstream SaaS
* Unified dashboard for onboarding status
* Error handling + email alerts on failure

---

# 📎 **License**

MIT License — free to use and modify.

---

# 🙌 **Contributions**

Pull requests are welcome!
If you’d like to contribute or report improvements, feel free to open an issue.

---

# 📬 **Contact**

Maintained by: **Chris Ejbich**
Senior Systems Administrator
GitHub: *chrisejbich*

---
