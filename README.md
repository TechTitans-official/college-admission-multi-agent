# College Admission Multi-Agent AI System

## AI Agent Module

### 1. Overview

The **College Admission Multi-Agent AI System** is an AI-based system designed to reduce the manual calling and follow-up work performed by college faculty during the admission process.

The AI agent system will communicate with prospective students and parents through **natural, two-way conversations**, provide verified college information, understand their responses, maintain their interaction state, and perform appropriate follow-up actions.

The system consists of **6 specialized AI agents**, where each agent performs a specific task in the admission workflow.

The main focus of this module is the **AI Agent layer**. The frontend dashboard, backend APIs, database, and administration interface will be integrated separately.

---

# 2. Objectives

The AI Agent module aims to:

* Automate repetitive admission-related communication.
* Reduce manual calling performed by faculty.
* Provide natural two-way voice conversations.
* Answer questions about the college using verified information.
* Understand candidate/parent responses and intentions.
* Classify candidates based on their interest.
* Avoid unnecessary repeated calls.
* Schedule callbacks when required.
* Send WhatsApp information after obtaining permission.
* Provide CAP Round information and reminders.
* Follow up based on CAP results.
* Maintain agent state and conversation context.
* Escalate complex questions to college staff when required.

---

# 3. Overall Agent Workflow

```text
                         CANDIDATE / PARENT
                                |
                                v
                       +-------------------+
                       |     AGENT 1        |
                       | INITIAL CALLING    |
                       +---------+---------+
                                 |
                 +---------------+---------------+
                 |               |               |
                 v               v               v
             INTERESTED     NOT INTERESTED    CALLBACK /
                 |                              NO ANSWER
                 v                                   |
        +-------------------+                         |
        |     AGENT 2       |<------------------------+
        | INTAKE & STATUS    |
        +---------+----------+
                  |
                  v
        +-------------------+
        |     AGENT 3       |
        | COLLEGE INFORMATION|
        +---------+----------+
                  |
                  v
        +-------------------+
        |     AGENT 4       |
        | WHATSAPP INFO     |
        +---------+----------+
                  |
                  v
        +-------------------+
        |     AGENT 5       |
        | CAP INFORMATION   |
        | & REMINDERS       |
        +---------+----------+
                  |
                  v
        +-------------------+
        |     AGENT 6       |
        | CAP RESULT        |
        | FOLLOW-UP         |
        +-------------------+
```

---

# 4. AI Agent Architecture

```text
                     VOICE / MESSAGE INPUT
                              |
                              v
                         PIPECAT
                              |
                              v
                    +------------------+
                    |       LLM        |
                    | Reasoning + NLP  |
                    +--------+---------+
                             |
              +--------------+--------------+
              |              |              |
              v              v              v
             RAG           TOOLS         MEMORY
              |              |              |
              v              v              v
       College Knowledge   Backend       Conversation
          Base / Qdrant     APIs           State
              |              |              |
              +--------------+--------------+
                             |
                             v
                       AGENT RESPONSE
                             |
                             v
                       VOICE / MESSAGE
```

### Main Technologies

| Component            | Technology                           |
| -------------------- | ------------------------------------ |
| Agent Framework      | Pipecat                              |
| LLM                  | Gemini / suitable realtime LLM       |
| Voice Processing     | Realtime audio pipeline              |
| RAG                  | Embeddings + Qdrant                  |
| Knowledge Base       | College documents                    |
| Agent Tools          | Backend APIs / Functions             |
| Memory               | Conversation state + candidate state |
| Language             | Python                               |
| Development          | Local environment                    |
| Production Telephony | Exotel / SIP or suitable provider    |
| Production Messaging | WhatsApp Business API                |

---

# 5. Agent 1 — Initial Admission Calling Agent

## Purpose

Agent 1 is responsible for making the initial admission-related call to prospective students or parents.

Its primary purpose is to introduce the college, understand whether the person is interested, answer basic questions, and determine the next action.

## Main Workflow

```text
Candidate Data
      |
      v
Check Previous Call History
      |
      +---- Already completed? ----> Do not call again
      |
      v
Make Voice Call
      |
      v
Introduce AI + College
      |
      v
Ask if it is a convenient time
      |
      +---- Busy ------> Schedule Callback
      |
      v
Explain College / Courses
      |
      v
Answer Questions
      |
      v
Understand Interest
      |
      +--------+-----------+------------+
      |        |           |            |
      v        v           v            v
 Interested  Not        Callback     No Answer
             Interested
      |
      v
Pass Candidate State to Agent 2
```

## Responsibilities

* Introduce the college.
* Explain why the call is being made.
* Ask whether the person has time to talk.
* Explain available diploma/degree courses.
* Explain basic college facilities.
* Answer questions using verified college information.
* Understand the person's response.
* Detect interest or lack of interest.
* Handle busy responses.
* Schedule callbacks.
* Handle unanswered calls.
* Avoid repeated calls.
* Record call outcome.
* Ask permission before sending WhatsApp information.

## Example Conversation

```text
AI:
Hello, I am an admission assistant calling on behalf of
XYZ College. Is this a good time to speak for a minute
about the admission process?

Parent:
Yes.

AI:
Thank you. Our college offers diploma and degree
programs in several areas. Are you currently looking
for admission for yourself or your child?

Parent:
My daughter is interested in Computer Engineering.

AI:
Great. I can provide information about the Computer
Engineering course, admission process and college
facilities. Would you also like me to send the
information through WhatsApp?

Parent:
Yes.

AI:
Sure. I will arrange that for you.
```

---

# 6. Agent 2 — Intake and Candidate Classification Agent

## Purpose

Agent 2 processes the outcome of Agent 1 and manages candidate classification and required follow-up information.

## Candidate States

```text
INTERESTED
NOT_INTERESTED
PROVISIONAL
CALLBACK_REQUIRED
NO_ANSWER
WRONG_NUMBER
HUMAN_REQUIRED
```

## Workflow

```text
Agent 1 Result
      |
      v
Agent 2
      |
      v
Analyze Structured Information
      |
      +-----------------------------+
      |             |               |
      v             v               v
 Interested    Provisional     Not Interested
      |             |               |
      +-------------+---------------+
                    |
                    v
             Store Candidate State
                    |
                    v
             Determine Next Agent
```

## Responsibilities

* Classify candidate status.
* Store course interest.
* Store callback requirements.
* Maintain interaction state.
* Detect incomplete information.
* Forward candidates to the appropriate next workflow.
* Prevent duplicate processing.

---

# 7. Agent 3 — College Information Agent

## Purpose

Agent 3 provides detailed and reliable information about the college.

The agent uses **RAG (Retrieval-Augmented Generation)** so that answers are based on approved college information.

## Knowledge Sources

```text
College Documents
       |
       +-- Courses
       +-- Facilities
       +-- Admission Process
       +-- Fees
       +-- Hostel
       +-- Placement
       +-- Achievements
       +-- FAQ
       +-- Other Approved Information
       |
       v
   Embeddings
       |
       v
     Qdrant
       |
       v
Relevant Information
       |
       v
      LLM
       |
       v
Agent Response
```

## Responsibilities

* Answer course-related questions.
* Explain college facilities.
* Explain admission requirements.
* Answer frequently asked questions.
* Provide verified information.
* Avoid making up information.
* Escalate unknown or sensitive questions to college staff.

---

# 8. Agent 4 — WhatsApp Information Agent

## Purpose

Agent 4 handles sending college information through WhatsApp after the candidate or parent provides permission.

## Workflow

```text
Agent 1
   |
   v
Ask for WhatsApp Permission
   |
   +---- No ----> Do not send
   |
   +---- Yes ---> Agent 4
                    |
                    v
              Send Information
                    |
                    +-- College Website
                    +-- Course Information
                    +-- Brochure
                    +-- Facilities
                    +-- Admission Information
                    |
                    v
             Candidate Replies
                    |
                    v
                   RAG
                    |
                    v
             Answer Questions
```

## Responsibilities

* Verify WhatsApp consent.
* Send approved college information.
* Send relevant links/documents.
* Answer WhatsApp questions.
* Use the college knowledge base.
* Maintain conversation context.
* Escalate questions when required.

---

# 9. Agent 5 — CAP Round Information and Reminder Agent

## Purpose

Agent 5 manages CAP Round information and reminders for interested and provisional candidates.

CAP dates will be provided and managed through the administration/backend system.

## Workflow

```text
Admin / Backend
      |
      v
CAP Dates
      |
      +---- CAP 1
      +---- CAP 2
      +---- CAP 3
      +---- CAP 4
      |
      v
Agent 5
      |
      v
Identify Relevant Candidates
      |
      v
Send Reminder
      |
      v
Answer CAP Questions
      |
      v
Update Candidate State
```

## Responsibilities

* Provide CAP Round information.
* Handle CAP 1 to CAP 4.
* Use admin-provided dates.
* Send reminders to relevant candidates.
* Prioritize interested/provisional candidates.
* Answer basic CAP-related questions.
* Track reminder status.
* Avoid sending duplicate reminders.

---

# 10. Agent 6 — CAP Result Follow-up Agent

## Purpose

Agent 6 processes CAP result information provided by the administration/backend and performs appropriate candidate follow-up.

## Workflow

```text
Admin Uploads CAP Result
          |
          v
      Agent 6
          |
          v
Match Candidate
          |
          v
Determine Result
          |
          v
Contact Candidate
          |
          v
Explain Result
          |
          v
Ask Admission Intention
          |
          v
Update Candidate State
```

## Responsibilities

* Receive CAP result data from backend.
* Match results with candidate records.
* Identify admission status.
* Communicate relevant result information.
* Follow up with candidates.
* Answer basic questions.
* Record candidate response.
* Notify/escalate to college staff when necessary.

---

# 11. Shared AI Capabilities

All agents can use common AI capabilities.

## 11.1 Natural Conversation

The agent should not behave like a simple recorded IVR.

It should:

* Listen to the response.
* Understand the meaning.
* Respond according to context.
* Handle interruptions.
* Ask follow-up questions.
* Handle unexpected questions.
* Maintain conversation context.
* Respond politely.

The objective is a **natural conversational experience**, not an exact imitation of a human.

---

## 11.2 RAG

RAG will be used to ground responses in verified college information.

```text
Question
   |
   v
Retrieve Relevant College Information
   |
   v
LLM
   |
   v
Grounded Response
```

The agent must not invent college information.

---

## 11.3 Tool Calling

Agents can call tools when an external action is required.

Example tools:

```text
check_previous_call()
record_call()
update_candidate_status()
schedule_callback()
get_college_information()
send_whatsapp()
get_cap_dates()
get_cap_result()
create_staff_alert()
```

The actual database/API implementation will be handled by the backend module.

---

# 12. Memory and State

Agents require state to maintain continuity.

## Conversation State

```text
Current conversation
Current question
Current intent
Current agent
Previous responses
```

## Candidate State

```text
Candidate ID
Name
Phone Number
Interest Status
Course Interest
Call Status
Call Attempts
Last Call
Callback Time
WhatsApp Consent
CAP Status
CAP Result
```

---

# 13. Call Management Logic

A major requirement is to prevent unnecessary repeated calls.

```text
Candidate
    |
    v
Check Call History
    |
    +---- Already contacted successfully
    |             |
    |             v
    |       Do not call again
    |
    +---- No answer
    |       |
    |       v
    |   Retry after configured time
    |
    +---- Callback requested
    |       |
    |       v
    |   Call at scheduled time
    |
    +---- Not interested
            |
            v
       Stop admission calls
```

The agent system should maintain call history and call status through the backend.

---

# 14. Human Escalation

The AI should not try to answer everything.

If a question is outside the verified knowledge base or requires human judgment:

```text
Candidate Question
       |
       v
Can AI answer reliably?
       |
    +--+--+
    |     |
   YES    NO
    |     |
    v     v
 Answer   Escalate
          to Staff
```

Examples:

* Special admission cases
* Complaints
* Payment disputes
* Questions requiring official confirmation
* Information unavailable in the knowledge base

---

# 15. Development Approach

The system will first be developed using free/low-cost development resources.

```text
             DEVELOPMENT
                  |
                  v
        Local Voice Agent
                  |
                  v
               Pipecat
                  |
                  v
          Development LLM
                  |
                  v
             RAG + Qdrant
                  |
                  v
          Backend Integration
                  |
                  v
             Testing
                  |
                  v
             PRODUCTION
                  |
                  v
      Production LLM / Services
                  |
                  v
          Telephony Provider
                  |
                  v
       Real College Phone Number
```

The model/provider should be replaceable through a clean interface so that changing from a development model to a production/paid model does not require rewriting the entire agent workflow.

---

# 16. Agent Development Order

The agents should not all be developed simultaneously.

Recommended order:

```text
1. Basic Pipecat Voice Agent
             ↓
2. Agent 1
             ↓
3. RAG
             ↓
4. Tool Calling
             ↓
5. Memory + State
             ↓
6. Agent 1 Testing
             ↓
7. Agent 2
             ↓
8. Agent 3
             ↓
9. Agent 4
             ↓
10. Agent 5
             ↓
11. Agent 6
             ↓
12. Integration
             ↓
13. Telephony
             ↓
14. Production Deployment
```

---
# 18. Future Production Integration

After the complete agent workflow is stable:

```text
Development Voice
       ↓
Testing
       ↓
Production Deployment
       ↓
Telephony Integration
       ↓
Exotel / SIP
       ↓
College Phone Number
```

The initial development environment will use a local microphone/speaker. Real phone calling will be integrated only after the voice agent workflow is working correctly.


# 20. Final System Vision

```text
                         COLLEGE ADMINISTRATION
                                  |
                                  v
                         Backend / Dashboard
                                  |
                                  v
                         AI AGENT SYSTEM
                                  |
        +-------------------------+-------------------------+
        |                         |                         |
        v                         v                         v
     Agent 1                   Agent 2                   Agent 3
   Initial Call            Intake / Status          College Information
        |                         |                         |
        +-------------------------+-------------------------+
                                  |
                                  v
                              Agent 4
                           WhatsApp Agent
                                  |
                                  v
                              Agent 5
                         CAP Reminder Agent
                                  |
                                  v
                              Agent 6
                         CAP Result Agent
                                  |
                                  v
                     Candidate Follow-up
```

