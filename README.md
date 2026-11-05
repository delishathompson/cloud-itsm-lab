# Cloud ITSM Lab

A small IT service desk I built in the cloud. ServiceNow handles the tickets. AWS watches one of my other projects and opens a ticket by itself when something breaks.

I built it to practice the everyday work of an IT support specialist: taking tickets, setting priority, sending them to the right team, tracking time promises (SLAs), writing help articles, handling approvals, and finding out why the same problem keeps coming back.

**Status:** Weekends 1, 2 and 3 are done **Built:** Oct-Nov 2026 **Cost:** $0 

## What is in this lab

Change \[ \] to \[x\] as you finish each one.

- [x] A service desk in ServiceNow with fake users and teams
- [x] Priority, ticket routing, and SLA timers
- [x] Three help articles (password, VPN, MFA)
- [x] Ten practice tickets worked from start to finish
- [x] Two reports
- [x] An AWS alarm that opens a ServiceNow ticket on its own
- [x] A software request form with manager approval
- [x] A problem record for repeated VPN tickets
- [x] Users, password resets, and MFA in Microsoft Entra ID

## How it fits together

There are two sides.

**The ticket side:** a person reports a problem or asks for something, a ticket is created in ServiceNow, it is sent to a team, and someone works it and closes it.

**The alarm side:**

```
CloudWatch alarm -> SNS -> Lambda -> ServiceNow -> Incident
```

CloudWatch is the part of AWS that watches things. If my music digest function has an error, CloudWatch sends a message. SNS passes the message along. A small Lambda function turns it into a ServiceNow incident. If the same alarm goes off again, the function adds a note to the same ticket instead of making a new one.

## Tools

| Tool | What I used it for | Cost |
| --- | --- | --- |
| ServiceNow Personal Developer Instance | The service desk | Free |
| AWS CloudWatch, SNS, and Lambda | Watching my project and making tickets | Free at this size \[check your bill\] |
| GitHub | Notes, screenshots, and backups | Free |
| Microsoft Entra ID Free | Practice with users and MFA | Free |

## Words I use in this lab

- **Incident:** something is broken.
- **Request:** someone is asking for something.
- **Problem:** the reason the same incident keeps happening.
- **SLA:** a promise about how fast a ticket will be handled.
- **Assignment group:** the team that owns a ticket.
- **Work notes:** notes on a ticket that the customer does not see.

## How I built it

### Weekend 1: the service desk

1. Got a free ServiceNow developer instance at developer.servicenow.com.
2. Made three groups: Service Desk, Network Support, Application Support.
3. Made fake users: three agents with the itil role, two end users, and one manager with the approver\_user role. I set the manager on both end users.
4. Learned how priority is worked out from impact and urgency.
5. Made a routing rule so tickets with the Network category go to Network Support.
6. Made two SLAs: P1 in 4 hours, P2 in 8 hours.
7. Wrote three help articles: reset a password, connect to the VPN, set up MFA on a new phone.
8. Worked ten practice tickets. On each one I filled in the caller, a short description, category, impact, urgency, work notes, and resolution notes.
9. Built two reports.
10. Exported my work and saved it in this repo, because free instances can be reclaimed.

### Weekend 2: AWS opens tickets

1. Made an integration user in ServiceNow called aws\_integration. I set Identity type to Machine and gave it the itil role.
2. Tested the connection from AWS CloudShell with a curl command before building anything else.
3. Made an SNS topic.
4. Made a Lambda function that reads the alarm message and creates or updates an incident. The password is stored in an environment variable, not in the code.
5. Connected the SNS topic to the function.
6. Made a CloudWatch alarm for errors on my music digest function.
7. Tested it with this command, which changes the alarm state without breaking anything:

```
aws cloudwatch set-alarm-state --alarm-name itsm-lab-music-digest-errors --state-value ALARM --state-reason 'Lab test' --region us-east-1
```

A ticket appeared. When I ran it again, the ticket got a new note and no second ticket was made. When the alarm went back to OK, it added another note.

### Weekend 3: a request form and a problem record

1. Made a catalog item called Software Access Request with two questions: software name and business reason.
2. Built a flow in Flow Designer. It starts from Service Catalog and asks the requester's manager for approval. If approved, it creates a task for the Service Desk. If rejected, it closes the request as incomplete.
3. Linked the catalog item to the flow on the item's Process Engine tab. The flow has to be active first.
4. Tested it as three people using Impersonate User. One user orders it, the manager approves it, and an agent finishes the task.
5. Made five VPN tickets, then one problem record: Remote users intermittently cannot connect to the VPN. I linked the five incidents to it from the Related Records tab on each incident, then added a workaround.

### Identity practice:

1. Made fake users and a group in Microsoft Entra ID.
2. Turned on MFA.
3. Reset a password.
4. Used those steps to close the matching ServiceNow tickets.

## What I would do next

- Move the ServiceNow password into a secrets service.
- Add a rule that turns incoming email into tickets.
- Connect Microsoft Entra ID and ServiceNow.
- Watch more than one of my projects.

## How I used AI

I used an AI assistant called Claude while working on this. Here is what each of us did.

**What the AI helped with**

- Planning the lab and the order of the steps
- Drafting the step-by-step instructions
- Drafting the Lambda code

**What I did**

- Set up the accounts and built everything myself in ServiceNow and AWS
- Ran every test and read the error messages
- Fixed what broke, based on what I saw on my screen
- Checked the official ServiceNow and AWS documentation when I was not sure
- Decided what to keep, change, or leave out

&#91;Add one or two sentences in your own words about what you learned from building it this way.\]

## Safety notes

- Every user, ticket, and email in this lab is fake.
- No passwords or keys are stored in this repo. The Lambda reads them from environment variables.
- Free ServiceNow instances can be reclaimed if they sit idle, so I log in every week and export my work.

## What is in this repo

```
cloud-itsm-lab/
  README.md
  screenshots/
  lambda/
    itsm_alarm_to_incident.py
  servicenow-exports/
    weekend-1-update-set.xml
    weekend-2-update-set.xml
    weekend-3-update-set.xml
    users.xml
    groups.xml
  knowledge-articles/
    reset-password.md
    connect-vpn.md
    set-up-mfa.md
```

&#91;Your name\] | \[Portfolio link\] | \[LinkedIn link\]
