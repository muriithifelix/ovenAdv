# Classified — ROOT.md

> **Document purpose:** Project-wide source of truth for product direction, architectural boundaries, implementation guardrails, and AI/developer working rules.
>
> **Status:** Working baseline compiled from decisions made during product definition. Items explicitly marked **OPEN**, **PROPOSED**, or **TO VALIDATE** are not approved requirements.
>
> **Product:** Classified  
> **Initial market:** Kenya  
> **Initial learner audience:** High-school learners  
> **Initial curriculum:** Kenya's Competency-Based Education (CBE/CBC)  
> **Initial currency:** KES  
> **Primary experience:** Responsive Progressive Web App (PWA)

---

## 1. How to use this document

This file is the project-wide implementation guide. Developers and AI coding agents must read it before changing product behavior, architecture, data structures, APIs, security controls, or design.

### 1.1 Document hierarchy

The project should maintain three complementary documents:

| Document | Owns | Must not silently override |
|---|---|---|
| `ROOT.md` | Product-wide principles, architecture boundaries, stack, security, implementation guardrails and cross-cutting decisions | Approved product requirements or design details without a documented decision |
| `PRD.md` | Functional requirements, user stories, workflows, business rules, priorities and acceptance criteria | Project-wide constraints in `ROOT.md` |
| `apple.design.md` | Existing visual design system, design tokens, visual principles and UX conventions | Product behavior in `PRD.md` or technical/security constraints in `ROOT.md` |

The existing design source is named **`apple.design.md`**. Do not rename it or assume it is called `DESIGN.md` unless the project owner explicitly approves that change.

### 1.2 Conflict resolution

1. `ROOT.md` governs cross-project architecture, security, privacy, implementation and agent constraints.
2. `PRD.md` governs what the product must do and how product workflows behave.
3. `apple.design.md` governs how the interface looks and how visual/interaction patterns are expressed.
4. Implementation must conform to all three.
5. If documents conflict, do not guess. Record the conflict, identify the affected requirements, and request an owner decision.
6. After an approved change, update every affected document in the same change set.

### 1.3 Status labels

- **CONFIRMED** — explicitly selected by the product owner.
- **PROPOSED** — recommended, but not explicitly approved.
- **OPEN** — requires a future decision.
- **TO VALIDATE** — requires legal, technical, operational, provider or user validation.

---

## 2. Product vision and definition

### 2.1 Vision

Transform the Kenyan classroom for the digital era by creating a learner-centred learning management system that improves the classroom experience through increased learner engagement, meaningful academic tracking, personalized revision and tutor engagement.

### 2.2 Product definition

**Classified** is a learner-centred digital learning platform designed initially for high-school learners in Kenya. It modernizes traditional LMS experiences by combining structured curriculum-aligned content, granular academic progress tracking, assessments, gamified learning, personalized revision recommendations and access to qualified tutors.

Classified is not a school-management or multi-school tenant LMS. Schools are not the launch customer or enrollment authority. Learners use the platform directly, and independent tutors register and offer permitted services.

### 2.3 Problems to address

- Traditional LMS products can present content without supporting sustained learner engagement.
- Learners need progress visibility at topic and subtopic level, not only broad subject-level summaries.
- Learners need to identify learning gaps and receive useful revision guidance.
- Interfaces should be responsive, accessible and engaging rather than bland or desktop-dependent.
- Learners need a safe, structured way to discover qualified tutors and arrange tutoring.

### 2.4 Product pillars

1. **Structured learning:** Curriculum-aligned subjects, topics, lessons, resources and assessments.
2. **Academic intelligence:** Topic-level and subtopic-level progress, assessment evidence and learning insights.
3. **Learner engagement:** Missions, points, levels, badges, streaks, challenges and optional leaderboards.
4. **Personalized revision:** Recommendations based on learning goals and low topic mastery; a rule-based study planner first, AI integration later.
5. **Tutor engagement:** Verified tutor discovery, topic matching, booking, payments, ratings and protected communication.
6. **Platform governance:** Content quality, assessment integrity, safeguarding, privacy, payments and administration.

### 2.5 Product principles

- Learner-centred, not content-dump-centred.
- Academic mastery is distinct from activity, completion and gamification.
- Learners retain control over optional sharing of their academic information.
- Tutor services require the specified verification and safeguarding controls.
- Central platform policy governs scoring, permissions and publication standards.
- Offline and low-bandwidth access are first-class requirements.
- Accessibility and privacy are foundational requirements.
- Prefer the simplest maintainable architecture that satisfies the requirements.
- Do not use AI where deterministic rules are sufficient.

---

## 3. Users, roles and access

### 3.1 Launch roles

- **Learner:** Uses learning content, assessments, progress tools, gamification and (with premium access) tutor discovery and booking.
- **Tutor:** Registers, submits verification evidence, selects subjects/topics, creates permitted supplementary content and assessments, offers tutoring and receives payouts.
- **Platform administrator:** Manages platform operations, accounts, subscriptions, payments, policies, curriculum, scoring configuration and platform-wide reporting.
- **Academic editor/reviewer:** Reviews academic accuracy, curriculum alignment, learning outcomes, question quality and assessment quality.
- **Moderator:** Handles assigned reports and routine moderation within assigned permissions.
- **Safeguarding officer:** Handles serious and escalated safeguarding cases.

Role permissions must be enforced by the backend. Hiding a button or route in the frontend is not authorization.

### 3.2 No guardian accounts at launch

**CONFIRMED:** Guardian accounts and guardian dashboards are not part of the initial release.

This does not remove the need for age-appropriate consent, safeguarding, privacy and compliance procedures for high-school learners. The applicable consent process is **OPEN / TO VALIDATE** and must be reviewed against Kenyan law and the actual age range served.

### 3.3 Learner eligibility

**CONFIRMED:** The initial audience is high-school learners. The architecture should not prevent future expansion to other education categories.

### 3.4 Tutor eligibility and verification

**CONFIRMED:**
- Anyone may register as a tutor applicant.
- Tutors submit academic certificates, teaching experience, professional credentials and identity evidence.
- A valid TSC registration is mandatory for a tutor to become verified and offer tutoring services.
- TSC verification is required; other evidence also requires appropriate administrative/manual verification.
- New tutors require approval before publishing content.
- Verified tutors may publish directly under platform rules and moderation.
- Verification, publishing permission and content moderation are separate controls.

**Implementation guardrails:**
- Do not treat TSC verification as proof of identity, qualification authenticity or teaching quality.
- Store verification status, evidence references, reviewer, timestamps and expiry/recheck state.
- Restrict access to identity and qualification documents.
- Do not invent an automated TSC integration. Confirm an authoritative verification route before implementing it.
- Reverification cadence, suspension and renewal rules are **OPEN**.

---

## 4. Curriculum and content architecture

### 4.1 Initial content hierarchy

**CONFIRMED initial hierarchy:**

`Curriculum → Grade → Subject → Topic → Subtopic → Lesson → Assessment`

The initial hierarchy is fixed for the first product definition. However, the underlying data model must support future curricula and configurable curriculum structures without requiring ordinary content changes to be hard-coded by developers.

### 4.2 Curriculum management

Administrators should be able to manage:
- Multiple curricula over time.
- Grades or levels.
- Subjects.
- Strands, sub-strands, topics and subtopics as appropriate to a curriculum.
- Learning outcomes and competencies.
- Curriculum versions, effective dates and archived structures.
- Mappings between learning content, assessments and curriculum elements.

The initial learner experience must preserve the confirmed hierarchy above. Future curriculum-specific structures should be handled through configuration and mappings rather than assumptions that every curriculum uses identical labels.

### 4.3 Content ecosystem

**CONFIRMED:** Hybrid content ecosystem:
- Central platform content is created by administrators, academic editors, contracted creators and qualified educators.
- Tutor-contributed content supplements the central library.
- Tutor contributions map to the same curriculum taxonomy.
- Content formats include text lessons/notes, PDFs, images/diagrams, embedded videos, interactive quizzes, MCQs, structured examinations, flashcards and practical assignments.

### 4.4 Tutor publishing

- New tutors require approval before publishing.
- Verified tutors can publish directly subject to policy.
- Verification does not guarantee academic correctness.
- Content can be flagged, reviewed, restricted or removed.
- All resources require curriculum metadata.
- Academic review and platform compliance are distinct.

### 4.5 Academic review and publication

**CONFIRMED:** Risk-based hybrid review:
- Academic editors review educational accuracy, curriculum alignment, learning outcomes, assessment quality and age-appropriateness.
- Administrators handle policy, copyright, permissions, publication compliance and reported content.
- Standard content receives academic review.
- Sensitive or high-impact content receives additional administrative review before publication.
- Review decisions and material changes are recorded.

Tutor direct-publishing rules remain as defined in section 4.4; do not accidentally impose the central-content workflow on every verified tutor submission.

### 4.6 Content versioning

**CONFIRMED:**
- Preserve historical versions.
- Learners normally see the latest approved version.
- Significant changes may trigger additional review.
- Administrators and academic editors can inspect previous versions.
- Preserve links between assessment attempts and the content/question version used where relevant.
- Do not rewrite historical learner evidence when content changes.

### 4.7 Ownership, licences and takedowns

**CONFIRMED principles:**
- Original creators retain ownership of their original work.
- Classified receives a defined licence to host, display and distribute contributed content while the creator participates on the platform.
- A limited post-removal transition period may apply for existing learner access, records and completed learning activities.
- Commissioned content may have separately agreed ownership terms.
- Third-party content requires appropriate permissions or licences.
- Record content provenance and applicable licence information.
- Provide copyright complaint, investigation, notification, response and appeal processes.
- Credible or urgent complaints may result in temporary restriction; other complaints may be investigated before restriction.
- Preserve relevant moderation and takedown records.

**OPEN:** Exact licence scope, duration, transition period, termination conditions, and retention of licensed copies. These require explicit terms and legal review; do not invent durations.

---

## 5. Learning, assessments and academic mastery

### 5.1 Assessment types

The following assessment types may contribute to mastery under centrally governed policy:
1. Official platform examinations.
2. Official platform quizzes.
3. Verified tutor assessments.
4. Assignments and practical tasks.
5. Timed mock examinations.
6. Tutor-created practice quizzes.

Assessment type weighting is centrally controlled by platform administrators. Tutors cannot set weights or directly alter mastery scores.

### 5.2 Assessment ownership and approval

- Administrators and authorized academic editors can create official platform assessments.
- Tutors create supplementary assessments under separate permissions.
- New tutors require approval before publishing assessments.
- Verified tutors may publish directly, subject to platform policy and moderation.
- Academic editors review assessment quality and curriculum alignment.
- Administrators can restrict, flag or remove assessments.
- Tutors cannot alter central scoring policies.

### 5.3 Question formats

Support:
- Multiple choice.
- True/false.
- Short answer.
- Essay.
- Matching.
- Fill-in-the-blank.
- Ordering/sequencing.
- File upload.
- Practical tasks and assignments.

Not every format is automatically markable. Human marking is required where judgment is needed.

### 5.4 Marking and partial credit

**CONFIRMED hybrid marking:**
- Automatically mark objectively scorable questions where reliable.
- Use tutor/editor marking for essays, practical tasks, assignments and responses requiring judgment.
- Support configurable rubrics for human-marked work.
- Support partial marks in both automatic and manual marking where the question and rubric permit it.
- Establish marking rules before results are calculated.
- Preserve marks, feedback, marking history and authorized corrections.

### 5.5 Question banks and metadata

**CONFIRMED:**
- Central question bank for official assessments, managed by administrators and authorized academic editors.
- Tutor-specific question banks for supplementary assessments.
- Question metadata includes curriculum, grade, subject, topic, subtopic, learning outcomes, competencies, difficulty, question type and cognitive skill level (e.g. Bloom's Taxonomy).
- Initial difficulty labels: Easy, Medium and Hard; administrators may configure the scale.
- Exact duplicate detection should be automatic.
- Semantically similar questions should be flagged for human review, not automatically deleted.
- Preserve question version history and historical attempts.

### 5.6 Question-bank security

Official examination questions require:
- Role-based access.
- Controlled exposure before release.
- Restricted exports and bulk extraction.
- Access and modification logs.
- Controlled randomized selection.
- Post-examination release controls for questions, answers and explanations.
- Backend enforcement of all access restrictions.

### 5.7 Randomization

**CONFIRMED configurable hybrid randomization:**
- Randomize question order and answer options where appropriate.
- Preserve fixed ordering where sequence matters or an examination requires standardized presentation.
- Do not randomize dependent sequences or linked instructions in a way that changes meaning.
- Preserve the actual order shown to each learner in their attempt record.
- Randomization must not alter marks, difficulty or weighting.

### 5.8 Examination mode and integrity

**CONFIRMED enhanced examination mode:**
- Configurable timers and automatic submission at expiry.
- Controlled navigation between questions and sections.
- Submission and attempt tracking.
- Randomization where configured.
- Suspicious-activity flags for administrative review.
- Preserve answers during temporary connectivity interruptions where technically possible.
- Treat flags as indicators for investigation, not proof of cheating.
- Do not implement invasive surveillance without a separate approved requirement and privacy review.

### 5.9 Connectivity and interruption recovery

**CONFIRMED hybrid approach:**
- Examinations are normally online.
- Save responses locally during temporary connectivity interruptions.
- Securely synchronize when connectivity returns.
- Prevent duplicate submissions.
- Record submission timestamps and synchronization events.
- Flag conflicts rather than silently overwriting responses.
- Keep deadlines and timers consistent.
- Brief interruptions may be handled automatically.
- Prolonged interruptions trigger a configurable recovery process and may require administrator review.
- Eligible technical failures may lead to recovery or rescheduling recommendations.
- Exceptional fresh attempts/rescheduling require authorized review under policy.
- Offline recovery must not bypass deadlines or examination controls.

### 5.10 Results, feedback and appeals

**CONFIRMED:**
- Automatic results may be released immediately or on schedule according to assessment settings.
- Manually marked results require marking/review before release.
- Official examinations may require authorized review before release.
- Correct answers and explanations follow release policy.
- Learners receive comprehensive feedback where permitted: total score, question-level marks, correct answers, explanations, learning outcomes assessed and revision recommendations.
- Authorized mark changes require reasons and timestamps.
- Learners can submit formal appeals through Classified.
- Original marker reviews first; unresolved cases escalate to an independent academic reviewer.
- Appeal outcomes and mark changes are auditable.
- Initial appeal window: 7 days after result release; configurable by assessment type.
- Learners receive in-platform and email notifications for appeal events, subject to notification rules.

### 5.11 Learner assessment analytics

Provide:
- Overall scores and assessment history.
- Topic/subtopic performance.
- Improvement trends.
- Question-level performance patterns.
- Strengths and areas for improvement.
- Cognitive skill performance using Bloom's Taxonomy.
- Comparisons between previous attempts.
- Targeted revision recommendations.

Analytics inform learning and recommendations; they do not independently alter mastery.

### 5.12 Mastery model

**CONFIRMED principles:**
- Mastery is based on weighted assessment performance.
- Mastery is separate from XP, streaks, levels, missions and other gamification.
- Gamification cannot increase academic mastery.
- Completion and mastery are separate: completing all lessons does not imply mastery.
- Topic completion can be high while mastery remains low.
- Prerequisites are guided, not blocking: learners can access topics, while the platform recommends revision for prerequisite gaps.
- Assessment weighting is centrally configurable.
- Preserve assessment evidence and policy versions; changes must not silently rewrite historical records.
- Invalidated assessments no longer contribute to current calculations, but historical records remain.
- Learners can inspect evidence behind mastery changes.

**PROPOSED, NOT YET APPROVED:** Five mastery labels and thresholds:
- 0–39: Beginning
- 40–59: Developing
- 60–79: Approaching Proficiency
- 80–89: Proficient
- 90–100: Advanced

These are not official CBC grading standards. Thresholds must be configurable and validated before launch.

**OPEN:** Exact weighting formula, category percentages, difficulty/reliability/attempt factors, repeat-attempt influence, eligibility rules for suspicious results, recalculation policy and final threshold values. Do not hard-code illustrative formulas or percentages as approved policy.

---

## 6. Recommendations and study planning

### 6.1 Recommendation architecture

Use distinct components:
1. **Mastery engine:** Calculates academic performance from eligible assessment evidence.
2. **Recommendation engine:** Identifies useful next steps from goals, mastery and configured rules.
3. **Study planner:** Converts goals, availability and learning priorities into weekly timetables and daily tasks.
4. **AI layer (later):** May personalize study-plan wording or suggestions after a separate approved phase.

AI must not independently determine or modify official mastery scores.

### 6.2 Launch recommendation triggers

**CONFIRMED triggers:**
- Learner's personal learning goals.
- Low topic mastery.

Other possible triggers—such as inactivity, upcoming examinations, prerequisite gaps or repeated poor performance—remain **OPEN** unless explicitly approved.

### 6.3 Study planner

**CONFIRMED:**
- The planner generates weekly timetables and daily tasks.
- Initial implementation is rule-based.
- AI integration comes later.
- Planner can use learner goals, low mastery and availability.
- It can recommend lessons and quizzes.
- The system must work without an AI provider.

**OPEN:** Plan editing, regeneration, conflict resolution, reminders, and how learner availability is collected.

### 6.4 Tutor recommendations

Recommend tutors specializing in topics where a learner has low mastery.

Matching factors:
- Topic-specific learner ratings.
- Verified qualifications.
- Teaching experience.
- Availability.

Tutor discovery filters may also include location and online/in-person format. Ranking must be explainable; no undisclosed pay-to-rank.

---

## 7. Tutor marketplace, booking and payments

### 7.1 Tutor verification

A tutor must have valid TSC registration to be verified and offer tutoring services. Identity, academic certificates and teaching experience also require review. Do not expose verification documents publicly.

### 7.2 Ratings

**CONFIRMED:** Overall 1–5 star rating with optional written feedback. Reviews should be linked to completed sessions. Topic-specific ratings may be derived by grouping session ratings by topic, but the exact presentation and minimum sample safeguards are **OPEN**.

### 7.3 Tutoring formats

**CONFIRMED:** Both online and in-person tutoring.
- Tutors specify supported formats.
- Online sessions use external meeting links (e.g. Google Meet or Zoom); Classified does not build video infrastructure at launch.
- Meeting details are accessible only to confirmed booking participants.
- Validate links and protect them from public exposure.
- In-person service locations must not expose personal residential addresses publicly.
- Apply age-appropriate safeguarding to in-person arrangements.

### 7.4 Booking flow

**CONFIRMED sequence:**
1. Learner submits availability.
2. Tutor proposes a suitable session time.
3. Learner accepts.
4. Learner pays.
5. Booking is confirmed.

Prevent double booking. Unpaid proposals should expire under configurable rules (**exact duration OPEN**). Keep booking history and payment state consistent.

### 7.5 No-show and cancellation

**CONFIRMED tutor no-show handling:**
- Learner can reschedule with the same tutor or choose another tutor without paying again.
- Tutor is flagged for review.
- Repeated no-shows can affect tutor eligibility and recommendations after investigation.
- Tutors must have an opportunity to explain.
- Do not automatically lower ratings while a dispute is unresolved.

**USER-SELECTED POLICY:** No cash refund for paid tutoring sessions. However:
- Tutor no-show must support a replacement/rescheduled session without another payment.
- The policy is subject to mandatory consumer-protection rights and applicable Kenyan law.
- Do not implement a blanket rule that overrides legal rights.
- Detailed learner cancellation rules, tutor cancellation rules and exceptional refund/dispute handling remain **OPEN / TO VALIDATE**.

### 7.6 Tutor earnings and payouts

**CONFIRMED:**
- Classified collects learner payments and manages transactions, earnings, payouts, disputes and records.
- Platform commission is deducted from completed-session earnings.
- Commission percentage is centrally configurable; exact percentage is **OPEN**.
- Tutors see gross earnings, commission and net payout.
- Earnings become eligible after session completion.
- On-demand withdrawal.
- M-Pesa only for tutor payouts.
- Verify payout number; use unique references, ledger entries, payout statuses and duplicate prevention.
- Reconcile failed payouts.
- Flagged/disputed sessions may be held.

### 7.7 Premium subscriptions

**CONFIRMED freemium model:**

Free:
- Core learning content.
- Basic quizzes and assessments.
- Topic-level mastery tracking.
- Basic recommendations.

Premium:
- AI study planner (when available).
- Weekly timetable and daily tasks.
- Tutor discovery and access.
- Scheduling.
- Advanced academic insights.

Tutoring sessions are paid separately from the premium subscription.

Other confirmed decisions:
- Monthly and annual plans.
- No free trial.
- Kenya launch, KES currency.
- M-Pesa and Visa/Mastercard for learner payments.
- M-Pesa through Safaricom Daraja API.
- Card gateway not selected.
- One standard monthly and one annual plan initially; administrators can configure pricing. Additional tiers/promotions may be introduced later.
- Price changes must not silently alter existing paid subscriptions.
- Cancellation stops renewal at the end of the paid billing period; premium remains active until then.
- Configurable grace period after failed renewal.
- On premium expiry, existing confirmed and paid tutor bookings remain valid; no new tutor bookings; AI planner becomes restricted; free content/quizzes/mastery remain; academic records are never deleted.

**OPEN:** Card provider, renewal retry policy, grace-period duration, subscription refunds, taxes/invoicing, exact prices, and provider-specific payment details.

### 7.8 Payment architecture

Keep internal payment logic provider-independent.
- Integrate Daraja for M-Pesa.
- Select a separate card gateway after evaluating Kenyan merchant eligibility, fees, recurring billing, dispute/refund support and API quality.
- Do not hard-code a card provider before approval.
- Payment flows require idempotency, verified provider callbacks, reconciliation and auditable transaction states.
- Do not rely on lightweight background tasks alone for critical payment verification or booking confirmation.

---

## 8. Gamification

**CONFIRMED advanced gamification at launch:**
- Points/XP.
- Levels.
- Badges.
- Streaks.
- Missions.
- Challenges.
- Leaderboards.
- Rewards.

Leaderboards:
- Global.
- Subject-based.
- Weekly challenge.

Guardrails:
- Rank by XP/challenge points, never by private academic mastery.
- Participation is optional.
- Use display names and protect identity.
- Do not expose private academic records.
- Apply anti-cheating controls.
- Periodic resets do not affect academic progress.
- Streaks must not punish breaks or encourage unhealthy screen time.
- Rewards should encourage learning, not excessive platform use.
- Keep gamification visually distinct from academic mastery.

---

## 9. Learner dashboard and discovery

### 9.1 Dashboard

Combined dashboard, with academic information taking priority:
- Mastery and subject/topic overview.
- Weak areas.
- Upcoming assessments.
- Recommendations.
- XP, level, badges, streak, missions, challenges and leaderboard.
- Continue learning and recent lessons.
- Daily tasks and weekly timetable.
- Personal insights, consistency, trends, goals and improvement.

Keep mastery, XP and activity as separate concepts.

### 9.2 Search and discovery

**CONFIRMED hybrid discovery:** Basic search, advanced filters and personalized recommendations.

Content search:
- Subjects, topics, lessons/resources, quizzes and revision materials.
- Filters: curriculum, grade, subject and content type.

Tutor search (premium learners):
- Topic specialization.
- Overall ratings.
- Verified qualifications.
- Teaching experience.
- Location.
- Availability.
- Online/in-person format.

Search results must respect visibility, permissions and tutor eligibility. No secret pay-to-rank.

### 9.3 Performance comparisons and privacy

**CONFIRMED:**
- Personal progress is primary.
- Optional comparisons against anonymized platform averages by subject, grade and topic.
- Use minimum group-size safeguards to reduce re-identification risk.
- No disclosure of other learners' names, marks or identifiable academic records.
- Academic comparisons are separate from XP leaderboards.
- Academic records are private by default.
- Learners can selectively share relevant progress with tutors.
- Learners can review and revoke optional sharing permissions, subject to necessary records and lawful retention.

Tutor access during a confirmed booking:
- Default: subject/topic, subtopic, mastery level and relevant assessment performance.
- Additional learner-approved context may include goals, revision history, strengths and weaknesses.
- Access begins when booking is confirmed and continues through the active booking.
- Post-session access default: 7 days, configurable.
- Access expires automatically; necessary booking, assessment and audit records remain under retention policies.

---

## 10. Communication and safeguarding

### 10.1 Communication

**CONFIRMED:** In-platform communication only between learners and tutors. Do not expose personal contact details.

- Messaging is linked to bookings/learning interactions.
- Both learner and tutor can initiate and respond during the permitted follow-up period.
- Post-session follow-up default: 7 days, configurable.
- Text, images and approved educational documents are supported.
- Approved formats initially include images, PDFs, Word documents and presentations; administrators can configure allowed types.
- Use file-size limits, type validation and security scanning.
- Store files in cloud object storage; store metadata and access references in PostgreSQL.
- Provide reporting and blocking.
- Ordinary user access expires after the communication period.
- Retain records for a configurable period for safeguarding, disputes and compliance.
- Exact retention period is **OPEN**.

### 10.2 Moderation

**CONFIRMED hybrid moderation:**
- Automated checks flag potentially inappropriate content.
- Users can report messages or conversations.
- Authorized human moderators investigate.
- Actions may include warnings, content restrictions, temporary messaging restrictions or escalation.
- Automated flags are not proof of misconduct.
- Notify users when messages are restricted or action is taken, where appropriate.
- Confidential monitoring/investigation may occur where necessary for safeguarding.
- Provide an appropriate review/appeal process without compromising safeguarding.

### 10.3 Safeguarding reporting and escalation

Users can report:
- Messages.
- Learner or tutor accounts.
- Tutoring sessions.
- Educational content.
- General safeguarding concerns.

Reports may be confidential from the reported user. Authorized safeguarding personnel may access reporter identity when necessary. Anonymous reporting may be considered separately where investigation remains effective.

Case statuses:
1. Received.
2. Under Review.
3. Escalated.
4. Action Taken.
5. Closed.

Provide appropriate status notifications without exposing confidential details.

Risk-based response targets:
- Urgent reports receive immediate attention.
- High-priority reports receive prompt review.
- Standard reports follow configurable response timelines.
- Overdue/unresolved cases may be escalated automatically.
- Exact response-time targets are **OPEN**.

### 10.4 Safeguarding roles and closure

- Trained moderators handle initial reports and assigned routine cases.
- A designated safeguarding officer handles serious and escalated cases.
- Administrators manage platform-level enforcement.
- Moderators may close routine cases within their permissions.
- Serious/escalated cases require safeguarding officer approval for closure.
- Administrators oversee enforcement decisions.
- Record evidence, decisions, actions and timestamps.
- Restrict sensitive case information to authorized personnel.

### 10.5 Safeguarding record retention and access

- Retention periods are configurable based on severity, law and safeguarding policy.
- Preserve records subject to active investigations, disputes or legal holds.
- Securely delete or anonymize when retention obligations end.
- Moderators see assigned cases only.
- Safeguarding officers access serious/escalated cases.
- Designated administrators access records only when necessary for authorized oversight.
- Log access and changes.

---

## 11. Admin dashboard and reporting

### 11.1 Admin capabilities

The administration dashboard must support:
1. Learner account management.
2. Tutor verification and management.
3. Content and assessment management.
4. Curriculum management.
5. Subscription and payment management.
6. Mastery weighting and scoring policies.
7. Tutor commissions and payouts.
8. Reports, analytics and platform monitoring.

Academic editors have separate academic review permissions.

### 11.2 Analytics

Platform analytics:
- Registrations and active users.
- Tutor counts and status.
- Subscriptions, cancellations and recurring revenue.
- Bookings and completion.
- Commissions and payouts.
- Payment failures and disputes.
- Content engagement.

Academic analytics:
- Subject/topic mastery trends.
- Difficult topics.
- Assessment performance.
- Completion rates.
- Recommendation effectiveness.
- Engagement and improvement over time.

Role-based reporting:
- Learners see their own progress.
- Tutors see authorized learner context and their own performance.
- Academic editors see academic/content insights.
- Administrators see platform-wide and authorized academic reports.

Use aggregated data wherever possible. Engagement and XP must not be reported as academic improvement.

---

## 12. Design system and user experience

### 12.1 Existing design foundation

**CONFIRMED:** Use the uploaded `apple.design.md` as the foundation and adapt it for education. Preserve its design tokens and principles; do not replace it wholesale.

Known design characteristics in the existing file include:
- Action Blue `#0066cc`.
- SF Pro Display and SF Pro Text typography.
- White, parchment and near-black surfaces.
- No decorative gradients.
- Minimal shadows.
- Pill-shaped buttons.
- Responsive breakpoints.

Adapt the system with education-specific patterns for:
- Learner dashboards.
- Assessment and examination screens.
- Tutor profiles.
- Progress charts.
- Gamification.
- Learning content and study plans.

Do not assume Apple branding or imply affiliation with Apple. Treat the file as a visual reference/system source only.

### 12.2 Responsive and accessible design

**CONFIRMED:**
- Responsive-first across phones, tablets and desktops.
- No essential desktop-only feature.
- Comprehensive accessibility from first release, targeting WCAG 2.2 AA.
- Keyboard navigation.
- Screen-reader support and semantic structure.
- Sufficient contrast and readable text.
- Alt text for meaningful images.
- Captions/transcripts for relevant media.
- Reduced-motion support.
- Accessible forms, labels and error states.
- Responsive accessibility, not merely responsive layout.

### 12.3 Localization

**CONFIRMED:** Launch in English with a multilingual-ready architecture. Kiswahili and other languages may be added later. Avoid hard-coded user-facing strings where localization would otherwise be difficult.

### 12.4 PWA and offline learning

**CONFIRMED:**
- PWA-first.
- Installable experience.
- Low-bandwidth support.
- Cached lessons and permitted resources.
- Offline synchronization when connectivity returns.
- Free/public materials may remain cached longer.
- Premium/restricted materials use configurable expiry and access checks.
- Respect content permissions and licences.
- Protect assessment integrity and prevent duplicate submissions/corrupted records.

---

## 13. Technical architecture and stack

### 13.1 Architecture style

**CONFIRMED:** Start with a modular monolith. Do not introduce microservices at launch. Split services only when measured scale, reliability or team boundaries justify the operational cost.

Use clear domain boundaries and avoid unnecessary cross-module coupling.

### 13.2 Confirmed technology stack

| Layer | Selected technology |
|---|---|
| Frontend | React |
| Frontend tooling | Vite |
| Styling | Tailwind CSS |
| Backend | Python + FastAPI |
| API style | REST, versioned under `/api/v1` |
| Database | PostgreSQL |
| ORM | SQLAlchemy |
| Migrations | Alembic |
| Frontend shared state | React Context API for appropriate global state |
| File storage | Cloud object storage; metadata in PostgreSQL |
| Video at launch | External embeds (e.g. YouTube/Vimeo); managed streaming later |
| Version control | Git + GitHub |
| Initial backend tests | pytest |
| Hosting | Managed frontend/backend and managed PostgreSQL |
| Client delivery | PWA |

### 13.3 Frontend architecture

- One modular React application initially.
- Separate learner, tutor and admin modules/layouts/routes.
- Permission-aware route and component rendering.
- Backend remains authoritative for all permissions.
- Use React Context for auth/user, subscription, theme and notification contexts as appropriate.
- Use local hooks/state for local UI state.
- Do not place all server data in global context.
- **OPEN:** Server-state fetching/caching library and exact frontend testing stack.

### 13.4 Backend architecture

- Organize FastAPI modules around domain responsibilities.
- Keep API schemas, business logic, persistence and external integrations separated.
- Use Pydantic validation.
- Use consistent response/error conventions.
- Provide OpenAPI documentation.
- Implement authentication, authorization, rate limiting and pagination/filtering consistently.
- Keep provider integrations behind internal interfaces.

### 13.5 Database organization

**CONFIRMED:** One PostgreSQL database with separate schemas for major domains.

Proposed logical schemas (names may be refined during schema design):
- `identity`
- `academic`
- `assessment`
- `mastery`
- `tutoring`
- `payments`
- `gamification`
- `communication`
- `administration`

These are domain boundaries, not a final table specification. Use SQLAlchemy and Alembic. Keep ownership clear, avoid unnecessary duplication and use transactions for consistency.

### 13.6 Background jobs

**CONFIRMED:** Start with FastAPI background tasks for suitable lightweight work. Add a dedicated task queue only when justified.

Critical payments, booking confirmation, payout and reconciliation must use reliable, idempotent workflows. Do not depend on an in-process background task as the sole mechanism for critical financial state transitions. Celery + Redis is a possible future option, not a launch requirement.

### 13.7 Authentication

**CONFIRMED hybrid user authentication:**
- Email/password.
- Google sign-in.
- Phone OTP as additional verification.
- Secure password hashing.
- Account verification and recovery.
- Role-based access.
- Session management.
- Stronger protection is recommended for privileged roles, but the current administrator-authentication decision is email/password only (see below).

**CONFIRMED administrator authentication choice:** Email and password for launch. Do not silently change this to mandatory 2FA. Record 2FA as a recommended future security enhancement because administrators can access sensitive records.

### 13.8 Administrator session management

**CONFIRMED:**
- Configurable inactivity timeout.
- Absolute session expiry.
- Revoke sessions after password changes.
- Revoke sessions when compromise is suspected.
- Secure logout/session invalidation.
- Record session activity for security auditing.

### 13.9 Failed-login protection

**OPEN:** The last question asked the user to choose failed-login protection. No answer was provided before the user requested completion. Recommendation was C: configurable rate limits, temporary lockouts, progressive delays and suspicious-login alerts. Do not mark this as approved; record as proposed.

### 13.10 Hosting and environments

**CONFIRMED:** Managed-cloud hosting:
- Managed frontend/backend services.
- Managed PostgreSQL.
- Automated backups.
- Separate development, staging and production environments.
- Environment-specific configuration and credentials.
- HTTPS.
- Rollback plan.
- Avoid vendor lock-in.

**OPEN:** Hosting provider and exact deployment topology.

### 13.11 File storage

**CONFIRMED:** Cloud object storage with metadata in PostgreSQL.
- Store PDFs, images, diagrams, documents and learning materials in object storage.
- PostgreSQL stores metadata, ownership, type, size, curriculum mapping, permissions and storage reference.
- Secure access, type/size validation, security scanning and CDN delivery as appropriate.

### 13.12 Video

**CONFIRMED hybrid video delivery:**
- Embed external videos at launch.
- Store links and metadata.
- Add managed video hosting/streaming later if justified.
- Keep video delivery independent from core learning modules.

### 13.13 Notifications

**CONFIRMED:** Email and in-app notifications. General SMS notifications are not planned initially. Phone OTP may require a separate SMS/OTP provider; this is not the same as general SMS notifications.

Notification events include results, bookings, reminders, subscriptions, payments, verification, password recovery, appeal and safeguarding status updates. Support preferences for non-essential notifications; do not let users disable essential security/transactional notices where required.

**OPEN:** Email provider, OTP provider, templates, delivery retries and exact notification preference categories.

### 13.14 Testing

**CONFIRMED progressive testing:**
- Initial backend unit tests with pytest.
- Frontend component tests.
- API integration tests.
- Database/model tests.
- Later: end-to-end, payment, authentication, load, security and regression tests.
- Critical feature coverage is required.
- Mastery/weighting changes must not silently rewrite historical outcomes.

**OPEN:** Exact frontend test tools, coverage targets and CI automation.

### 13.15 Version control and deployment

**CONFIRMED:**
- Git locally and GitHub centrally.
- Clear commit messages.
- `.gitignore`; never commit secrets.
- Separate dev/prod configuration.
- README and release notes.
- Manual deployment at launch.
- Run tests before deployment.
- Review migrations.
- Keep rollback instructions.
- GitHub Actions/automated CI/CD may be added later.

Branch protection, pull-request policy and release approval rules are **PROPOSED** pending explicit approval.

---

## 14. Security, privacy and safeguarding guardrails

Security baseline:
- HTTPS.
- Secure password hashing.
- Authentication and backend-enforced role-based authorization.
- Password recovery and account verification.
- Input validation and protection against common web vulnerabilities.
- Secure secret management through environment configuration.
- Database backups.
- Restricted access to tutor identity documents and learner records.
- Secure file access and validation.
- Audit logs for sensitive actions.
- Appropriate data minimization and retention.
- Safeguarding controls for learner/tutor communication and in-person sessions.

The user selected an essential security baseline and email/password-only administrator authentication for launch. Additional controls such as mandatory administrator 2FA, encryption of sensitive stored data and expanded audit logging are recommendations, not silently approved product decisions. Before launch, perform a security and privacy review and document any required controls.

Because the platform serves high-school learners:
- Apply age-appropriate privacy and safeguarding.
- Do not expose learner contact details or private academic records.
- Restrict tutor access to relevant learner information.
- Provide reporting, blocking and escalation.
- Review Kenyan data-protection, child-protection, consumer-protection and copyright obligations with qualified professionals.
- Do not treat the absence of guardian accounts as absence of consent obligations.

---

## 15. AI and automation guardrails

### 15.1 Launch approach

**CONFIRMED:** Rule-based study planner first; AI integration later.

The launch product must operate without an AI provider. Do not add AI dependencies or costs unless separately approved.

### 15.2 AI boundaries for future phases

- AI may assist with personalization of study plans after approval.
- AI must not change official mastery scores.
- AI-generated guidance must be distinguishable from official curriculum content and verified assessment feedback.
- Keep an abstraction boundary around AI providers/models.
- Monitor usage and costs.
- Provide deterministic fallback behavior.
- Do not send unnecessary sensitive learner data to external AI providers.
- AI features require privacy, safety, accuracy and age-appropriateness review.

---

## 16. Implementation instructions for developers and AI coding agents

### 16.1 Before making changes

1. Read `ROOT.md`, `PRD.md` and `apple.design.md`.
2. Identify the relevant product requirement and acceptance criteria.
3. Identify affected domains, database schemas, APIs, permissions and user roles.
4. Check whether the requested behavior is **CONFIRMED**, **PROPOSED**, **OPEN** or **TO VALIDATE**.
5. If a necessary decision is open, ask the project owner instead of inventing a rule.
6. Prefer the smallest maintainable implementation that satisfies approved requirements.

### 16.2 Non-negotiable agent rules

AI coding agents and developers must not:
- Rename the product from Classified.
- Treat schools as platform tenants or launch customers.
- Introduce microservices without an approved architectural decision.
- Change the selected stack without approval.
- Add dependencies, paid services or external providers without explaining the need and obtaining approval where material.
- Invent pricing, commission percentages, legal terms, retention durations, scoring weights or eligibility rules.
- Let tutors alter official mastery calculations or central assessment weights.
- Let XP, streaks or missions increase academic mastery.
- Bypass backend authorization by relying on frontend visibility.
- Expose learner private data, tutor verification documents or safeguarding records.
- Automatically treat moderation or suspicious-activity flags as proof of misconduct.
- Silently overwrite historical assessment attempts, content versions or financial records.
- Store secrets in source control.
- Make destructive database changes without reviewed migrations and a rollback plan.
- Generate or modify code when the project owner has requested documentation-only work.

### 16.3 Change workflow

For every material change:
1. State the problem and relevant requirement.
2. Identify affected modules, schemas, APIs and user flows.
3. Check compatibility with the stack and domain boundaries.
4. Update the relevant documentation.
5. Propose the smallest coherent change.
6. Add/update tests for expected behavior and failure cases.
7. Review security, privacy, accessibility and safeguarding impact.
8. Review migration and rollback impact.
9. Summarize changed files, decisions and unresolved risks.

### 16.4 Database and API change rules

- Use Alembic migrations for schema changes.
- Never modify production data structure manually.
- Preserve historical assessment, payment, booking, content and moderation records.
- Use transactions and idempotency for critical state transitions.
- Version REST APIs under `/api/v1`.
- Validate inputs at API boundaries.
- Enforce authorization in backend service paths.
- Document breaking changes and migration steps.
- Do not create cross-domain coupling without a clear reason.

### 16.5 UI implementation rules

- Follow `apple.design.md` tokens and principles.
- Adapt the design for learning workflows; do not copy unrelated product layouts blindly.
- Maintain responsive behavior across phone, tablet and desktop.
- Meet the WCAG 2.2 AA target.
- Provide loading, empty, error, success and permission-denied states.
- Keep academic mastery visually distinct from XP and gamification.
- Ensure core workflows work on low-bandwidth connections.

### 16.6 Testing and release rules

- Add tests with each meaningful feature.
- Test permission boundaries, not just happy paths.
- Test offline recovery and duplicate submission protection.
- Test payment idempotency and reconciliation.
- Test historical-record preservation when policies or content change.
- Run migrations against staging before production.
- Deploy manually at launch after tests and migration review.
- Maintain rollback instructions and release notes.

---

## 17. MVP and rollout

**CONFIRMED:** MVP-first strategy and controlled pilot before public launch.

Candidate MVP capabilities (priority and exact scope still need approval):
- Authentication and profiles.
- Curriculum/content browsing.
- Lessons and learning resources.
- Assessments and marking.
- Academic mastery and learner dashboard.
- Tutor verification and management.
- Admin and academic-editor workflows.
- Payments foundation where required.
- PWA/offline learning essentials.
- Safeguarding and privacy controls.

Do not assume every advanced marketplace, AI or analytics feature must ship in the first pilot. The project owner must approve a prioritized MVP scope and acceptance criteria.

Release approach:
1. Development.
2. Staging with test data.
3. Controlled pilot.
4. Resolve quality, safety, performance and usability issues.
5. Public launch only after release criteria are met.

---

## 18. Open decisions register

The following items must be resolved before the relevant feature is treated as implementation-ready.

### 18.1 Product and policy
- Final MVP scope and priority order.
- Exact learner age range and applicable consent process.
- Whether/how guardian consent is obtained without guardian accounts.
- Exact tutor verification evidence review, TSC verification route and rechecks.
- Licence duration/scope and post-removal transition period.
- Content appeal and takedown timelines.
- Content retention and deletion policy.

### 18.2 Assessment and mastery
- Final mastery thresholds and labels.
- Exact scoring/weighting formula and category weights.
- Difficulty, reliability and attempt factors, if used.
- Repeated-attempt influence and treatment of targeted revision.
- Eligibility and review rules for suspicious results.
- Recalculation and policy-version rules.
- Exact exam interruption thresholds and recovery policy.
- Question-bank retention and exam-release policy details.
- Assessment appeal exceptions and review service levels.

### 18.3 Tutor marketplace and payments
- Platform commission percentage.
- Card gateway.
- Exact subscription prices.
- Payment retry and grace-period durations.
- Tutor proposal expiry.
- Learner cancellation and tutor cancellation rules.
- Legally compliant refund/dispute policy.
- M-Pesa payout operational details and reconciliation procedures.
- Tutor rating display safeguards and topic-rating minimum sample size.

### 18.4 Communication and safeguarding
- Conversation retention duration.
- Safeguarding record retention periods by case type.
- Moderation response targets.
- Safeguarding response and escalation timelines.
- Appeal process for messaging restrictions.
- File size limits and final approved file types.
- Moderation tooling and authorized access procedures.
- In-person tutoring safety procedures.

### 18.5 Technical implementation
- Hosting provider.
- Cloud object-storage provider.
- Email provider.
- Phone OTP provider.
- Card payment provider.
- Frontend server-state library and test stack.
- Search/indexing implementation.
- Monitoring, error tracking and analytics providers.
- Video hosting/streaming provider for a later phase.
- Background queue trigger/threshold.
- Exact backup retention and restore objectives.
- Administrator failed-login protection (proposed recommendation: rate limits, temporary lockouts, progressive delays and suspicious-login alerts).
- Whether/when to introduce administrator 2FA.
- Branch/PR protections and automated CI.

### 18.6 Documents and governance
- Confirm whether `apple.design.md` remains the permanent design filename.
- Review and align the actual `PRD.md` when supplied.
- Approve the document hierarchy and conflict-resolution rules.
- Establish a change-approval owner and versioning convention.

---

## 19. Decision log summary

This summary records the major choices made during the planning conversation. The detailed rules above take precedence.

| Area | Recorded decision |
|---|---|
| Product | Classified; learner-centred high-school learning platform in Kenya |
| Business model | Direct learner platform; independent tutors; no school tenancy |
| Guardian accounts | Not at launch |
| Curriculum | CBE/CBC initial; fixed initial hierarchy with configurable curriculum management |
| Content | Hybrid central and tutor-contributed ecosystem |
| Tutor publishing | New tutors require approval; verified tutors may publish directly under moderation |
| Content review | Risk-based academic and administrative review |
| Content versions | Preserve history; learners see latest approved version |
| Content licence | Creator retains ownership; defined platform licence and limited transition period |
| Copyright | Hybrid report, investigation, restriction, response and appeal |
| Assessments | Official and tutor supplementary assessments |
| Question types | Comprehensive formats |
| Marking | Automatic and human marking; configurable partial credit |
| Question banks | Central official bank plus tutor-specific banks |
| Difficulty | Configurable; Easy/Medium/Hard initially |
| Question tagging | Comprehensive curriculum, outcome, competency and cognitive-skill metadata |
| Duplicate detection | Exact automatic detection; semantic similarity flagged for human review |
| Exam security | Role access, controlled exposure, restricted exports, logs and release controls |
| Exam mode | Enhanced timed mode, navigation controls and suspicious-activity flags |
| Connectivity | Online with temporary offline recovery |
| Interruption policy | Brief automatic recovery; prolonged configurable review |
| Results | Configurable release; comprehensive feedback |
| Appeals | Original marker then independent reviewer; 7-day default; configurable |
| Mastery | Weighted assessment performance; separate from gamification and completion |
| Recommendations | Goals and low mastery; rule-based planner first |
| Study planner | Weekly timetable and daily tasks |
| Tutor matching | Topic ratings, qualifications, experience and availability |
| Tutor eligibility | Valid TSC mandatory plus identity, qualifications and experience checks |
| Tutoring format | Online and in-person |
| Booking | Learner availability → tutor proposal → learner acceptance → payment → confirmation |
| No-show | Reschedule/replace without additional payment; tutor flagged for review |
| Refunds | User-selected no-cash-refund policy, subject to applicable law and no-show replacement |
| Tutor payouts | After completed session; on-demand M-Pesa |
| Premium | Monthly/annual, no trial; M-Pesa and cards; KES |
| Gamification | Advanced; global, subject and weekly leaderboards; XP not mastery |
| Discovery | Search, filters and personalized recommendations |
| Dashboard | Combined academic and gamification, academic priority |
| Privacy | Private by default; learner-controlled sharing with tutors |
| Messaging | In-platform only; 7-day post-session default; attachments; moderation |
| Safeguarding | Risk-based reporting, escalation, dedicated safeguarding officer |
| Admin analytics | Platform and academic analytics with role-based access |
| Design | Adapt `apple.design.md`; preserve its tokens and principles |
| Accessibility | WCAG 2.2 AA target |
| Localization | English launch; multilingual-ready |
| Delivery | PWA-first, low-bandwidth, offline support |
| Architecture | Modular monolith |
| Frontend | React + Vite + Tailwind |
| Backend | Python + FastAPI |
| Database | PostgreSQL, SQLAlchemy, Alembic |
| DB organization | One database, separate domain schemas |
| API | REST `/api/v1` |
| State | React Context for appropriate shared state |
| Files | Cloud object storage; metadata in PostgreSQL |
| Video | External embeds first; managed streaming later |
| Background jobs | FastAPI tasks initially; dedicated queue when justified |
| Auth | Email/password + Google; phone OTP additional verification |
| Admin auth | Email/password selected; 2FA is future recommendation |
| Hosting | Managed cloud; dev/staging/prod |
| Testing | Progressive testing, pytest backend initially |
| Version control | Git + GitHub |
| Deployment | Manual initially |
| Rollout | MVP-first, controlled pilot |

---

## 20. Final instruction to implementation agents

**Classified must be implemented as a maintainable, secure, accessible, learner-centred platform—not as a collection of disconnected features.**

Preserve the confirmed decisions in this document. Treat all unresolved items as unresolved. Do not invent policy, pricing, scoring, legal or provider details. When a requirement is ambiguous or conflicts with another project document, stop and request clarification. Keep `ROOT.md`, `PRD.md` and `apple.design.md` aligned as the project evolves.
