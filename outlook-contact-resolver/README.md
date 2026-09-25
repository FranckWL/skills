# outlook-contact-resolver

A [Joule Work Desktop](https://www.sap.com) skill that automatically resolves a recipient's email address from Outlook history, asks for confirmation, then hands off to email composition — all in one seamless flow.

---

## Overview

When a user mentions sending an email to a named person, this skill:

1. **Extracts** the recipient's name from the user's message
2. **Searches** the Outlook email history to find known email addresses for that person
3. **Confirms** the address with the user (with the option to correct or override)
4. **Composes** a professional email and generates a clickable `mailto:` link that opens directly in Outlook

---

## Trigger Phrases

The skill activates on phrases such as:

| Language | Examples |
|---|---|
| French | `"Envoie un email à Jean Dupont"`, `"Écris un message à Marie Martin"`, `"Je veux contacter Thomas Leroy"`, `"Mail pour Pierre Bernard"` |
| English | `"Send an email to Anna Schmidt"`, `"Write to John Doe"`, `"Email Sarah about the project"` |

---

## Workflow

```
User mentions a named recipient
        │
        ▼
Step 1 — Extract recipient name
        │
        ▼
Step 2 — Search Outlook history (search_emails)
        │
        ├─ 1 address found   → present & confirm
        ├─ Multiple found     → list & let user choose
        └─ None found        → ask user to provide address
        │
        ▼
Step 3 — User confirms / corrects address
        │
        ▼
Step 4 — Compose email + generate mailto: link
```

---

## Tools Used

| Tool | Purpose |
|---|---|
| `search_emails` | Search Outlook history by name or sender |
| `list_emails` | Browse recent emails if needed |
| `get_email` | Read full email content to extract contact details |

> **Note:** This skill relies on **Outlook email history** to resolve contacts. It does not query the Global Address List (GAL) directly. For new contacts with no prior exchange, the user is asked to provide the address manually.

---

## Installation

### Via Joule Work Desktop

1. Open Joule Work Desktop
2. Go to **Skills** → **Install from file**
3. Select the `SKILL.md` file from this repository

### Via GitHub / Hub

Import or install the skill directly from the Joule Desktop Hub if published there.

---

## Requirements

- **Joule Work Desktop** with Outlook integration enabled
- Microsoft Outlook account connected to Joule

---

## Configuration

No additional configuration is required. The skill uses the connected Outlook account automatically.

---

## Example Interaction

```
User:   Send an email to Sophie Renard about the Q3 review meeting

Joule:  I found the following address for Sophie Renard:
        sophie.renard@company.com
        Is that correct? Confirm, or provide a different address.

User:   Yes, that's correct.

Joule:  What is the subject and the key points to include?

User:   Subject: Q3 Review Meeting — Agenda, key points: date confirmation,
        slides to prepare, attendees list.

Joule:  [Draft email displayed]
        → Open in Outlook: mailto:sophie.renard@company.com?subject=...
```

---

## Version

`1.0.0`

---

## Tags

`email` · `outlook` · `contacts` · `composition` · `communication`

---

## Author

Franck LAY — SAP Solution Architect, Banking & Insurance

---

## License

Licensed under the [Apache License 2.0](./LICENSE).

You are free to use, modify, and distribute this skill under the terms of the Apache 2.0 License.
A copy of the license is included in this repository.
