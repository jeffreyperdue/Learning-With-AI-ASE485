# AI Prompts and Responses

- **Week 11:** 3/23/2026 - 3/29/2026
- **Focus:** Analyzing Testing Methods in Context
- **AI Consulted:** Copilot & Claude

## Prompt

Analyze the information about user testing methods provided in the attached document (`user-testing-methods.md`). Generate a table with an analysis of the best combinations of testing methods for the business side of my NomNom Safe app. The table should include an explanation for why the combination was selected and how/why it will be useful specifically for testing NomNom Safe. Make sure to take the following information into account when selecting methods:

- I will only be conducting tests on the business side of the app, the current state of which is an MVP
- I will be conducting tests with real food-service business owners
- I am a full-time college student with 3 weeks left to complete the testing
- It may be too difficult to schedule in-person testing sessions
- I can use AI as necessary to set up the testing environment, but I don't want to rely on it for conducting tests
- I plan to look into using Claude MCP to set up the testing environment

## Response - Claude

Generated an HTML page (`nomnom-testing-analysis.html`)

## Response - Copilot

> ### Recommended Combinations Table
>
> | Combination                                                             | Methods Included                                         | Why This Combo (with citations)                                                                                                                                                                                                             | Why It Fits NomNom Safe Specifically                                                                                                                                                                                                                        |
> | ----------------------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
> | **1. Remote Moderated Usability Testing + Post‑Session Interviews**     | Moderated usability testing + user interviews            | Moderated testing gives deep qualitative insight and lets you probe the “why” behind behavior . Interviews add context about motivations and expectations, which the document notes are ideal for early discovery and post‑test debriefing. | Perfect for testing complex workflows like onboarding and menu creation — both highlighted as ideal for moderated sessions. Remote sessions avoid scheduling in‑person meetings. You only need 5 users to uncover ~85% of issues (Nielsen Norman Group) .   |
> | **2. Unmoderated Remote Testing + Short Survey**                        | Unmoderated remote testing + surveys                     | Unmoderated testing is fast, low‑cost, and works when tasks are straightforward and stable . Surveys provide scalable quantitative feedback and satisfaction scores (SUS/NPS) .                                                             | Great for validating stable flows like “add allergen info,” “edit menu item,” or “view dashboard.” Business owners can complete tasks on their own time — essential given their unpredictable schedules. Surveys help you quickly quantify friction points. |
> | **3. Heuristic Evaluation + Moderated Usability Testing**               | Expert heuristic review + moderated testing              | Heuristic evaluation is fast and inexpensive, catching obvious issues early before involving users . Moderated testing then validates real‑world behavior.                                                                                  | You can use AI (e.g., Claude MCP) to run a heuristic pass automatically before human testing. This reduces wasted time for business owners and ensures your MVP is test‑ready.                                                                              |
> | **4. Prototype/Flow Review (AI‑Assisted) + Unmoderated Remote Testing** | AI‑driven synthetic testing + unmoderated remote testing | AI‑driven synthetic testing catches “obvious mistakes” instantly and at zero recruitment cost . Unmoderated testing then validates with real users.                                                                                         | Ideal for your 3‑week timeline. You can rapidly iterate using AI between human test rounds. This ensures business owners only see improved versions, respecting their limited availability.                                                                 |
> | **5. Closed Beta (Small Group) + Analytics/Session Recording**          | Closed beta + heatmaps/session recordings + analytics    | Beta testing reveals real‑world performance issues and edge cases across devices and browsers . Heatmaps and session recordings show friction points visually .                                                                             | Business owners use the app in their natural environment (kitchen, office). You get passive data without scheduling sessions. Great for validating whether the MVP is stable enough for real‑world use.                                                     |
> | **6. Tree Testing + Moderated Usability Testing**                       | Tree testing + moderated testing                         | Tree testing validates whether your navigation structure matches user expectations and mental models . Moderated testing then checks whether the UI supports those expectations.                                                            | NomNom Safe’s business dashboard (menus, allergens, business info, analytics) must be findable. Tree testing ensures your IA isn’t “developer‑logic.” Moderated testing confirms whether owners can complete tasks without confusion.                       |
> | **7. Surveys + Beta Testing**                                           | Surveys + closed beta                                    | Surveys are scalable and inexpensive for gathering broad sentiment . Beta testing provides real‑world usage insights and edge‑case discovery.                                                                                               | Business owners can use the MVP during real shifts. Surveys help you quantify satisfaction and identify priority improvements before launch.                                                                                                                |
>
> ---
>
> ### Which Combination Should You Use First?
>
> Given your constraints (3 weeks, remote, real business owners, MVP), the **optimal sequence** is:
>
> #### **Week 1: Heuristic Evaluation + AI‑Assisted Prototype Review**
>
> - Fastest way to clean up obvious issues.
> - Uses AI for setup, not for conducting tests.
>
> #### **Week 2: Remote Moderated Usability Testing (5 users) + Short Interviews**
>
> - Deep qualitative insight.
> - Only 5 users needed to uncover 85% of issues (document citation).
>
> #### **Week 3: Unmoderated Remote Testing + Short Survey + Light Analytics**
>
> - Validates fixes at scale.
> - Requires minimal scheduling.
>
> This sequence balances:
>
> - depth
> - speed
> - remote‑friendliness
> - your limited time
> - respect for business owners’ schedules
