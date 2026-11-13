Technical Documentation

Customer Support Automation

1. System Architecture

The system is built with n8n and consists of four independent workflows that share data through n8n Data Tables.

The architecture separates ticket creation, status management, delay detection, and reporting into different processes.

2. Workflow Details

2.1 Customer Support Automation

Purpose: Receive and process new customer support requests.

Process:

Receive customer information through the Customer Support Request form.

Read the subject and problem description.

Analyze the text using a JavaScript Code node.

Assign a priority level.

Save the ticket in the CustomerTickets Data Table.

Send a confirmation email to the customer.

Route the ticket based on its category.

Assign the responsible support team.

Send an email notification to the assigned team.

Priority Classification:

The system uses keyword-based classification:

PriorityExample keywordsUrgenthacked, security breach, stolen, cannot access, cannot login, account lockedImportantpayment, billing, refund, error, failedNormalNo matching urgent or important keywords 

Urgent keywords have higher priority than Important keywords.

2.2 Ticket Status Management

Purpose: Allow support agents to update existing tickets.

Process:

Receive the ticket ID, new status, and agent note through the Status Update Form.

Retrieve the existing ticket.

Update its status, agent note, and updated timestamp.

Insert a record into TicketHistory.

Check whether the new status is Resolved.

If resolved, save the resolution timestamp.

Send a status update email to the customer.

Supported status values:

Open

In Progress

Resolved

The status update form supports In Progress and Resolved.

2.3 Delayed Ticket Detection

Purpose: Identify unresolved tickets that have remained open for more than three days.

Process:

Schedule Trigger starts the workflow daily at 08:00.

Retrieve ticket records from CustomerTickets.

Calculate the elapsed time since ticket creation.

Check whether the ticket is older than three days.

Exclude tickets with Resolved status.

Update the isDelayed field.

Detection rule:

A ticket is delayed when:

Its age is greater than three days.

Its status is not Resolved.

Otherwise, isDelayed is set to false.

2.4 Ticket Reporting

Purpose: Generate a daily summary of customer support activity.

Process:

Retrieve all tickets from CustomerTickets.

Process the records using JavaScript.

Calculate ticket statistics.

Calculate resolution and delay rates.

Generate a report summary.

Send the report to the manager by email.

Report metrics:

Total Tickets

Open Tickets

In Progress Tickets

Resolved Tickets

Delayed Tickets

Resolved Rate

Delayed Rate

The percentage calculations include a zero-ticket check to prevent division by zero.

3. Database Design

3.1 CustomerTickets

Stores the main ticket records.

FieldDescriptionidAutomatically generated ticket IDcustomerNameCustomer namecustomerEmailCustomer email addresssubjectTicket subjectcategoryTechnical, Billing, or GeneraldescriptionProblem descriptionpriorityNormal, Important, or UrgentstatusCurrent ticket statuscreatedAtTicket creation timestampassignedTeamResponsible support teamagentNoteSupport agent's noteupdatedAtLast update timestampresolvedAtResolution timestampisDelayedDelayed ticket indicator 

3.2 TicketHistory

Stores ticket status change records.

FieldDescriptionticketIdRelated ticket IDactionAction performedoldStatusPrevious statusnewStatusNew statusagentNoteAgent's notechangedAtChange timestamp 

4. JavaScript Implementation

JavaScript is used in the Code nodes for data processing and business logic.

Main applications include:

Reading ticket subject and description.

Keyword-based priority classification.

Calculating ticket age.

Detecting delayed tickets.

Filtering tickets by status.

Calculating report statistics and percentages.

5. Email Integration

The workflows use SMTP email integration to communicate with customers, support teams, and managers.

Email notifications include:
Customer ticket confirmation.

Support team notifications.

Customer status update notifications.

Daily management reports.

Email credentials must be configured separately in the user's n8n instance.

6. Execution and Testing

All four workflows were tested during development.

The tests covered:

Normal, Important, and Urgent ticket classification.

Ticket category routing.

Ticket creation and data storage.

Status updates and history records.

Customer email notifications.

Delayed ticket detection.

Resolved ticket exclusion.

Reporting calculations and email delivery.

7. Configuration Requirements

Before running the workflows:

Install or access a running n8n instance.

Import the workflow JSON files.

Create the required CustomerTickets and TicketHistory Data Tables.

Configure SMTP credentials.

Verify form and workflow settings.

Activate the scheduled workflow for automatic daily execution.

8. Security Considerations

Configure credentials inside n8n rather than hardcoding passwords.

Do not publish private customer information.

Use appropriate access permissions for forms and workflows.

Review exported workflow files before sharing them publicly.
