# Product Requirements Document (PRD)

**Project:** [Project Name]  
**Version:** 1.1 ELITE  
**Date:** February 17, 2026  
**Status:** 🟢 Active | 🟡 In Review | 🔴 Blocked | ✅ Complete  
**Owner:** [Product Owner Name]  
**Engineering Lead:** [Tech Lead Name]  
**Designer:** [Designer Name]

---

## 📋 Quick Reference (TL;DR)

**What:** [One sentence: What are we building?]

**Why:** [One sentence: Why does this matter?]

**Success Metric:** [Primary KPI with target value]

**Timeline:** [Target launch date]

**Dependencies:** [Critical blockers or prerequisites]

---

## 1. Problem Statement & Context

### The Problem
[Describe the current pain point or opportunity in 2-3 sentences. Be specific about who experiences this problem and when.]

**Example:**
> Advertisers managing 50+ Google Ads campaigns spend 10+ hours/week manually pausing low-performing campaigns and adjusting bids. This manual work costs $5,000+/month in labor while campaigns continue spending during the review lag, wasting ~15% of ad budget.

### Why Now?
[Explain why this is the right time to solve this problem. Market changes? New technology? Competitive pressure?]

### Who Has This Problem?
- **Primary User:** [Title, context, frequency]
- **Secondary Users:** [If applicable]
- **Anti-Users:** [Who should NOT use this?]

### Current State (Without This Feature)
[Describe the existing workaround or manual process]

### Future State (With This Feature)
[Describe how life will be better after this is built]

---

## 2. Goals & Success Metrics

### North Star Metric
**[Primary metric that defines success]**

Target: [Specific number with timeframe]

### Primary Goals
1. **Goal 1:** [Specific, measurable]
2. **Goal 2:** [Specific, measurable]
3. **Goal 3:** [Specific, measurable]

### Anti-Goals (What We're NOT Doing)
- ❌ [Out of scope item 1] - *Why: [Reason]*
- ❌ [Out of scope item 2] - *Why: [Reason]*

---

## 3. User Stories & Acceptance Criteria

### Epic 1: [Epic Name]

#### User Story 1.1: [Story Title]
**As a** [type of user]  
**I want** [some goal/desire]  
**So that** [some reason/benefit]

**Acceptance Criteria:**
- [ ] **Given** [initial context]  
      **When** [action taken]  
      **Then** [expected outcome]

- [ ] **Given** [initial context]  
      **When** [action taken]  
      **Then** [expected outcome]

**Priority:** 🔴 P0 (Must Have) | 🟡 P1 (Should Have) | 🟢 P2 (Nice to Have)

---

## 4. Functional Requirements

### Core Features

#### Feature 1: [Feature Name]
**Description:** [What it does in 1-2 sentences]

**Requirements:**
- **FR-1.1:** [Specific requirement]
  - Input: [Trigger]
  - Processing: [Action]
  - Output: [Result]
- **FR-1.2:** [Specific requirement]

---

## 5. Non-Functional Requirements

### Performance
- **NFR-P1:** Page load time <2 seconds (p95)
- **NFR-P2:** API response time <500ms (p95)

### Security
- **NFR-S1:** All API endpoints require authentication
- **NFR-S2:** Input validation on all user inputs

---

## 6. Technical Specifications

### Architecture Overview
[High-level diagram or description of system architecture]

### Google Ads Script Specifications (if applicable)

#### Script: [Script Name]
**Purpose:** [What it does]

**Execution Frequency:** [Hourly, Daily, Weekly]

**GAQL Query:**
```javascript
const query = `
  SELECT 
    campaign.id,
    campaign.name,
    metrics.cost_micros,
    metrics.conversions
  FROM campaign
  WHERE campaign.status = 'ENABLED'
    AND segments.date DURING LAST_30_DAYS
  LIMIT 10000
`;
