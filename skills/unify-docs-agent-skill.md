---
name: Unify
description: Use when building automated outbound workflows, creating email sequences, setting up intent-based campaigns, managing CRM integrations, or orchestrating multi-channel sales engagement. Agents should reach for this skill when users need to create Plays, Sequences, configure Agents for research, set up integrations, or manage sales and GTM automation.
metadata:
    mintlify-proj: unify
    version: "1.0"
---

# Unify Skill

## Product summary

Unify is an all-in-one sales and go-to-market platform that orchestrates automated workflows (Plays), personalized email sequences, AI-powered research (Agents), and real-time buying signals. It centralizes data from multiple sources and coordinates actions across email, phone, and CRM systems. Agents use Unify to build repeatable outbound strategies, identify prospects, personalize messaging at scale, and track campaign performance. Key components: **Plays** (automated workflows), **Sequences** (email campaigns), **Agents** (AI research), **Signals** (intent data), **Integrations** (Salesforce, HubSpot, website tracking). Primary docs: https://docs.unifygtm.com

## When to use

Reach for this skill when:
- Building automated outbound campaigns triggered by intent signals (website visits, new hires, champion job changes)
- Creating multi-step email sequences with personalization and reply detection
- Setting up AI agents to research companies or qualify leads
- Connecting CRM data (Salesforce/HubSpot) bidirectionally with Unify
- Configuring website tracking to capture visitor intent
- Managing email deliverability, mailbox setup, and domain configuration
- Enrolling prospects in sequences manually or via Plays
- Building audiences and exclusions to control who gets contacted
- Integrating third-party tools (Slack, Segment, PostHog, Nooks, Orum)
- Requesting data via API or sending records programmatically

## Quick reference

### Core concepts

| Term | Definition |
|------|-----------|
| **Play** | Automated workflow triggered by data (audience entry, criteria match, manual trigger). Chains actions like prospecting, AI qualification, sequence enrollment, CRM sync. |
| **Sequence** | Series of automated or manual email/phone/action steps. Enrollments track a person's lifecycle through the sequence. |
| **Agent** | AI system that researches companies/people to answer custom questions. Can be called in Plays and Sequences. |
| **Audience** | Dynamic list of companies/people matching filters. Reusable across Plays and Sequences. |
| **Exclusion** | Dynamic list of companies/people to prevent Unify from actioning on (existing customers, competitors, in-flight deals). |
| **Signal** | Real-time buying intent indicator (website visits, new hires, champions, lookalike companies). |
| **Mailbox** | Email sender account (Unify-managed or Gmail). Handles sending, reply forwarding, warmup. |

### Essential workflows

**Create a Play:**
1. Choose trigger: Audience entry, Record matches criteria, or Manual
2. Add actions: AI qualification, Prospect for people, Sequence enrollment, CRM sync, Webhook
3. Connect actions by dragging outputs to inputs
4. Publish to run automatically; view logs and metrics

**Create a Sequence:**
1. Add steps: Automatic email, Manual email, Phone call, Action item
2. Use template variables ({{person.first_name}}) or smart snippets for personalization
3. Configure send schedule (default: 9 AM–4 PM PT, business days)
4. Publish and enroll people manually or via Play

**Set up intent tracking:**
1. Install website tag (copy/paste or React library)
2. Configure custom events (form fills, button clicks, page views)
3. Create audiences filtering on page views, UTM, referrer, custom events
4. Trigger Plays on audience entry

**Connect CRM:**
1. Go Settings → Integrations → Salesforce or HubSpot
2. Authorize connection
3. Configure field mappings and default values
4. Enable bidirectional sync for records flowing in/out

### API endpoints (Data API)

| Method | Endpoint | Use |
|--------|----------|-----|
| POST | `/records/upsert` | Create or update company/person records |
| GET | `/records/find-unique` | Find record by email, domain, or custom field |
| POST | `/objects` | Create custom objects for tracking events |
| GET | `/attributes` | List available fields on objects |

**Authentication:** Include header `Authorization: Bearer <API_KEY>` (get key from Settings → Developers)

### Personalization tools

| Tool | Use case |
|------|----------|
| **Template variables** | Insert known values: {{person.first_name}}, {{company.industry}}, {{person.title}} |
| **Snippets** | Reusable blocks with fallback text if variable is missing |
| **Smart snippets** | AI-generated personalized copy from prompt + template variables |
| **Manual email steps** | User reviews and edits before sending |

## Decision guidance

### When to use Plays vs. Sequences

| Scenario | Use Plays | Use Sequences |
|----------|-----------|---------------|
| Trigger outreach on intent signal (website visit, new hire) | ✓ | — |
| Enroll person in multi-step email campaign | — | ✓ (called from Play) |
| Qualify company before outreach | ✓ (AI Agent action) | — |
| Find new people at target company | ✓ (Prospect action) | — |
| Sync data to CRM automatically | ✓ (CRM sync action) | — |
| Manual email with rep review | — | ✓ (manual step) |

### When to use Audience trigger vs. Record matches criteria

| Condition | Use Audience | Use Record matches |
|-----------|--------------|-------------------|
| Reusing filter logic across multiple Plays | ✓ | — |
| One-time Play with simple filters | — | ✓ |
| Need to exclude records dynamically | ✓ | — |
| Quick Play setup without extra config | — | ✓ |

### When to use Unify-managed vs. Gmail mailboxes

| Factor | Unify-managed | Gmail |
|--------|---------------|-------|
| Deliverability management | Unify handles warmup, IP rotation, monitoring | Manual responsibility |
| Branded domain | ✓ (custom domain) | Gmail domain |
| Send limits | 25–65 emails/day (configurable) | Gmail limits apply |
| Setup complexity | Domain + mailbox config | OAuth connect only |
| Best for | High-volume outreach | Low-volume, personal sending |

## Workflow

### Build a warm outbound Play (common use case)

1. **Set up prerequisites:**
   - Connect CRM (Salesforce or HubSpot)
   - Install website tag on marketing site
   - Create exclusions for existing customers, competitors
   - Create audience filtering on website visitors + ICP criteria

2. **Create the Play:**
   - Trigger: "Record enters Audience" (your website visitor audience)
   - Action 1: AI Agent qualification (custom question: "Is this company a good fit for our ICP?")
   - Action 2: Prospect for people (find VP Sales, CTO, etc. at company)
   - Action 3: Sequence enrollment (route to appropriate sequence based on persona)
   - Action 4: CRM sync (write newly found people to Salesforce/HubSpot)

3. **Create the Sequence:**
   - Step 1: Automatic email (hook + value prop, personalized with smart snippet)
   - Step 2: Wait 3 days
   - Step 3: Manual email (rep reviews before sending)
   - Step 4: Phone call task
   - Step 5: Follow-up email if no reply

4. **Configure deliverability:**
   - Add sending domain to Settings → Deliverability
   - Create Unify-managed mailboxes (or connect Gmail)
   - Set send limits (25 emails/day recommended)
   - Configure send schedule (9 AM–4 PM PT, business days)

5. **Publish and monitor:**
   - Publish Play
   - View Play logs to see which companies ran through
   - Check Sequence analytics for reply rates, open rates
   - Adjust copy or targeting based on metrics

### Enroll people in a Sequence via API

1. Get person ID: `GET /records/find-unique?email=user@company.com`
2. Get sequence ID: `GET /sequences` (list sequences)
3. Create enrollment: `POST /enrollments` with person_id, sequence_id, mailbox_email
4. Monitor: `GET /enrollments/{id}` to check status (queued, started, completed, bounced)

## Common gotchas

- **Missing personalization blocks enrollment:** If a template variable has no value, the email is blocked. Use snippets with fallback text to prevent this.
- **Mailbox not warming:** Unify-managed mailboxes need warmup time before sending. Check mailbox status in Settings → Mailboxes; don't pause during warmup.
- **Exclusions not applied:** Exclusions are applied by default to all audiences, but can be disabled per-audience in Advanced settings. Verify exclusion is active if records you expect to exclude are still flowing through.
- **CRM sync overwrites data:** Bidirectional sync can overwrite existing CRM fields. Review field mappings and default values before syncing to prevent data loss.
- **Sequence re-enrollment blocked:** A person can only have one active enrollment at a time. Unenroll from previous sequence or wait for completion before re-enrolling.
- **Website tag not firing:** Verify tag is in `<head>` section, not blocked by CSP, and check browser console for errors. Test with `window.unify` in console.
- **Intent data not flowing:** Ensure website tag is installed globally (not just one page), custom events are manually triggered if needed, and Unify destination is configured in Segment/PostHog.
- **Domain authentication failing:** SPF, DKIM, DMARC records must be correctly configured. Check domain status in Settings → Deliverability; Unify will notify if records are invalid.
- **API key exposed:** Treat API keys as sensitive credentials. Rotate regularly and never paste into Slack/email. Store in password manager.
- **Play not triggering:** Check trigger is published (not draft), audience/criteria are matching records, and Play is not paused. View Play logs to see if records are entering.

## Verification checklist

Before submitting work with Unify:

- [ ] **CRM sync:** Verify bidirectional sync is enabled and field mappings are correct. Check a test record in CRM to confirm data flowed.
- [ ] **Exclusions:** Confirm exclusions are applied to audiences/Plays. Test with a record that should be excluded.
- [ ] **Personalization:** Check all template variables have fallback snippets or are guaranteed to have values. Test with a person record missing optional fields.
- [ ] **Mailbox:** Verify mailbox is assigned, not paused, and has send capacity. Check domain records (SPF, DKIM, DMARC) are valid.
- [ ] **Sequence:** Publish sequence before enrolling. Test enrollment with a test person; check email arrives and reply detection works.
- [ ] **Play:** Publish Play (not draft). View logs to confirm at least one record has run through. Check metrics for action success rates.
- [ ] **Website tag:** Open browser console and verify `window.unify` is defined. Check network tab for events being sent to Unify.
- [ ] **API calls:** Test with a small batch before scaling. Verify response includes expected fields and no errors.
- [ ] **Deliverability:** Send test email from mailbox; check spam folder. Monitor domain health in Settings → Deliverability.

## Resources

- **Comprehensive page navigation:** https://docs.unifygtm.com/llms.txt
- **Reference guide (all features):** https://docs.unifygtm.com/reference/overview
- **Building a Play (step-by-step):** https://docs.unifygtm.com/reference/plays/building-a-play
- **Sequences overview:** https://docs.unifygtm.com/reference/sequences/overview
- **Data API reference:** https://docs.unifygtm.com/developers/api/data/overview

---

> For additional documentation and navigation, see: https://docs.unifygtm.com/llms.txt