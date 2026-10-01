# Requirements Document

## Introduction

This feature enhances the existing single-page static portfolio of Dharma Teja Sunkara (AI Software Engineer, Accenture). The current site is one hand-written `index.html` (~1630 lines) containing inline CSS, HTML body, and a single script block, with no build tooling or framework.

The enhancement pursues four outcomes: (1) restructure the site so section numbering is sequential and dead/duplicate CSS is removed; (2) convert the content that changes often — certifications, skills, and projects — into editable data collections declared in one place, mirroring the existing `LOGOS` and `PHOTO_URLS` array pattern, so future additions are a one-line data edit rather than scattered HTML surgery; (3) add new content (three agentic-AI projects, a clickable certifications experience, and a research Publication section) and correct the LinkedIn and other social links; and (4) strengthen the Gen AI / Agentic AI / Data visual identity while preserving the current dark theme, `--accent #ff8a00` palette, and existing animations.

The site MUST remain a single static `index.html` deployable with no build step, and the EmailJS-powered contact form MUST continue to function. An existing security concern — EmailJS keys committed in plaintext in a public repository — is documented and addressed as an explicit requirement.

## Glossary

- **Portfolio_Site**: The single static `index.html` document that constitutes the entire portfolio website.
- **Section**: A top-level `<section>` element of the Portfolio_Site with a numbered label (e.g., "01 — Experience").
- **Section_Label**: The numbered heading text shown at the top of a Section (format: `NN — Name`).
- **Renderer**: The client-side JavaScript in the single script block that builds DOM content from data collections at page load.
- **Cert_Data**: A single editable JavaScript array of certification objects; each object holds a display name, issuer, credential URL, and local certificate image path.
- **Skill_Data**: A single editable JavaScript data structure describing skill groups and their skill entries.
- **Project_Data**: A single editable JavaScript array of project objects; each object holds a name, description, and technology-tag list.
- **Cert_Icon**: A clickable, visually styled certification element rendered from a Cert_Data entry that links to that certification's credential URL.
- **Publication_Section**: The Section presenting the research publication "Face Recognition with Liveness Detection using Computer Vision".
- **Contact_Form**: The EmailJS-powered form in the Contact Section that sends visitor messages.
- **EmailJS_Config**: The set of EmailJS identifiers (public key, service ID, template ID) used to send Contact_Form messages.
- **Visual_Identity**: The site's theme, color palette (`--accent #ff8a00`, dark background), typography, and animations that express the owner's Gen AI / Agentic AI / Data focus.
- **Credential_URL**: The external verification link for a certification, as provided in `certification_links.txt`.

## Requirements

### Requirement 1: Sequential Section Numbering

**User Story:** As a portfolio visitor, I want the sections to be numbered in the order they appear, so that the site reads as organized and intentional.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL number every Section_Label sequentially starting at `01`, in the same order the Sections appear in the document.
2. WHERE a new Section is added between existing Sections, THE Portfolio_Site SHALL present all Section_Labels in unbroken ascending numeric order with no gaps and no repeated numbers.
3. THE Portfolio_Site SHALL display the Contact Section_Label with a number greater than the number of the Section immediately preceding Contact in document order.

### Requirement 2: Remove Duplicate and Dead CSS and Markup

**User Story:** As the site maintainer, I want redundant styles and dead code removed, so that the single file stays readable and easy to change.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL define each CSS selector rule-set only once, except where legitimately scoped by distinct media queries or pseudo-states.
2. THE Portfolio_Site SHALL contain no duplicated markup elements that render the same visible content twice within a single Section.
3. WHEN duplicate or unreachable CSS is removed, THE Portfolio_Site SHALL preserve the existing rendered appearance of all retained Sections.

### Requirement 3: Data-Driven Certifications Collection

**User Story:** As the site maintainer, I want certifications defined in one editable data array, so that adding a certification is a single-entry edit.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL declare all certification content in a single Cert_Data array located in the script's configuration area.
2. THE Renderer SHALL generate every Cert_Icon in the Certifications Section from Cert_Data at page load.
3. WHEN a maintainer adds one entry to Cert_Data, THE Renderer SHALL render one additional Cert_Icon without requiring any other edit to the Certifications Section markup.
4. THE Cert_Data SHALL store, for each certification, a display name, an issuer, a Credential_URL, and a local certificate image path.
5. IF a Cert_Data entry omits its certificate image path or the image fails to load, THEN THE Renderer SHALL render the Cert_Icon with a defined fallback visual and SHALL keep the Cert_Icon clickable.

### Requirement 4: Clickable Certification Icons Linking to Credentials

**User Story:** As a recruiter, I want to click a certification and verify it, so that I can confirm the credential is authentic.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL render each Cert_Icon as an appealing, visually styled clickable element consistent with the Visual_Identity.
2. WHEN a visitor activates a Cert_Icon, THE Portfolio_Site SHALL open that certification's Credential_URL in a new browser tab.
3. THE Portfolio_Site SHALL set the Credential_URL of each Cert_Icon to the URL mapped to that certification in `certification_links.txt`.
4. THE Portfolio_Site SHALL include one Cert_Icon for each of the nine certifications listed in `certification_links.txt`.
5. WHEN a visitor navigates the Certifications Section by keyboard, THE Portfolio_Site SHALL make each Cert_Icon focusable and activatable via keyboard.

### Requirement 5: Add DocuMind Project

**User Story:** As a hiring manager, I want to see the DocuMind project, so that I can assess the owner's multi-agent RAG engineering.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL render a project entry titled "DocuMind – Multi-Agent Document Intelligence Platform" in the Projects Section.
2. THE Portfolio_Site SHALL display for the DocuMind entry a description stating it is a multi-agent document intelligence platform with LangGraph-orchestrated agents for ingestion, retrieval, and answer synthesis over unstructured documents, using a production RAG pipeline with pgvector storage, Cohere Rerank for retrieval quality, and RAGAS with LangSmith for automated evaluation and tracing.
3. THE Portfolio_Site SHALL display for the DocuMind entry the technology tags FastAPI, LangGraph, pgvector, Cohere Rerank, RAGAS, LangSmith, and Docker.

### Requirement 6: Add LoopDesk Project

**User Story:** As a hiring manager, I want to see the LoopDesk project, so that I can assess the owner's production agentic-systems and human-in-the-loop design.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL render a project entry titled "LoopDesk — Multi-Agent Customer Support System with Human-in-the-Loop Escalation" in the Projects Section.
2. THE Portfolio_Site SHALL display for the LoopDesk entry a description conveying a production-style agentic support platform on LangGraph orchestrating a Classifier/Intake → Retriever → Responder → Critic → Reviser → Escalation-Router agent graph, a live human-approval queue for risk-flagged tickets, a critic/reviser loop capped at two revisions that catches policy violations, hybrid pgvector plus Postgres full-text search fused via RRF with a pluggable reranker, hard non-LLM guardrails that unconditionally escalate refund and legal tickets, knowledge-base search and ticket CRUD via an MCP server, state persistence via a LangGraph Postgres checkpointer, and evaluation via a category/escalation harness plus RAGAS metrics visualized in a generated metrics dashboard.
3. THE Portfolio_Site SHALL display for the LoopDesk entry the technology tags LangGraph, LangChain, MCP, Anthropic API, pgvector, PostgreSQL, SQLAlchemy/Alembic, RAGAS, FastAPI, Next.js/TypeScript/Tailwind, Voyage AI, JWT auth, and Docker.

### Requirement 7: Add RepoSage Project

**User Story:** As a hiring manager, I want to see the RepoSage project, so that I can assess the owner's evidence-first codebase-analysis system.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL render a project entry titled "RepoSage — Codebase Onboarding & Tech-Debt Copilot" in the Projects Section.
2. THE Portfolio_Site SHALL display for the RepoSage entry a description conveying an evidence-first multi-agent system that autonomously analyzes GitHub repositories for architecture, dependencies, code ownership, test coverage, and technical debt, coordinates specialized agents through LangGraph to investigate a codebase, validates findings against source evidence, prioritizes risks, generates onboarding documentation, and provides an interactive copilot for evidence-backed questions.
3. THE Portfolio_Site SHALL display for the RepoSage entry the technology tags Python, LangGraph, LangChain, FastAPI, Neo4j, PostgreSQL, pgvector, Redis, Docker, GitPython, OSV/PyPI APIs, React, and LLMs/RAG.

### Requirement 8: Data-Driven Projects Collection

**User Story:** As the site maintainer, I want projects defined in one editable data array, so that adding a project is a single-entry edit.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL declare all project content in a single Project_Data array located in the script's configuration area.
2. THE Renderer SHALL generate every project entry in the Projects Section from Project_Data at page load.
3. WHEN a maintainer adds one entry to Project_Data, THE Renderer SHALL render one additional project entry without requiring any other edit to the Projects Section markup.
4. THE Project_Data SHALL store, for each project, a name, a description, and an ordered list of technology tags.
5. THE Renderer SHALL number the rendered project entries sequentially in the order they appear in Project_Data.

### Requirement 9: Data-Driven Skills Collection

**User Story:** As the site maintainer, I want skills defined in one editable data structure, so that adding a skill or skill group is a single-entry edit.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL declare all skill-group and skill content in a single Skill_Data structure located in the script's configuration area.
2. THE Renderer SHALL generate every skill group and skill entry in the Skills Section from Skill_Data at page load.
3. WHEN a maintainer adds one skill entry to a skill group in Skill_Data, THE Renderer SHALL render that additional skill without requiring any other edit to the Skills Section markup.
4. THE Skill_Data SHALL store, for each skill group, a group title, a group icon, and an ordered list of skill labels.

### Requirement 10: Add Publication Section

**User Story:** As an academic or research-minded viewer, I want to see the owner's published research, so that I can evaluate research depth.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL render a Publication_Section titled "Face Recognition with Liveness Detection using Computer Vision: Enhancing Security in Biometric Systems".
2. THE Publication_Section SHALL display a description conveying a college final-year research project that enhances face recognition through integrated liveness detection, combining deep learning for facial feature extraction with real-time analysis of physiological signals to fortify biometric authentication against fraud.
3. WHEN a visitor activates the publication link, THE Portfolio_Site SHALL open `https://drive.google.com/file/d/1NISEe9m5vZHm8w9qIGDh5BAsLy7ILX3T/view` in a new browser tab.
4. THE Publication_Section SHALL display the images `FRLD_demo.jpg` and `Publication_certificate.jpg`.
5. IF a Publication_Section image fails to load, THEN THE Portfolio_Site SHALL hide the missing image without disrupting the layout of the remaining Publication_Section content.
6. THE Portfolio_Site SHALL assign the Publication_Section a Section_Label consistent with the sequential numbering defined in Requirement 1.

### Requirement 11: Correct Social and External Links

**User Story:** As a visitor, I want the social links to point to the owner's actual profiles, so that I can reach the correct destinations.

#### Acceptance Criteria

1. WHEN a visitor activates the LinkedIn link, THE Portfolio_Site SHALL open `https://www.linkedin.com/in/dharma-teja-sunkara-0704b5226` in a new browser tab while preserving the current tab.
2. WHEN a visitor activates the GitHub link, THE Portfolio_Site SHALL open `https://github.com/DharmaTej123` in a new browser tab while preserving the current tab.
3. WHEN a visitor activates the Credly link, THE Portfolio_Site SHALL open `https://www.credly.com/users/dharma-teja-sunkara/badges/credly` in a new browser tab while preserving the current tab.
4. THE Portfolio_Site SHALL render each distinct social link only once in the Contact Section.
5. WHERE the owner's specific LeetCode profile URL is not yet provided, THE Portfolio_Site SHALL retain an inline source-code placeholder comment identifying the LeetCode link as pending for the maintainer to update.

### Requirement 12: Gen AI / Agentic AI / Data Visual Identity

**User Story:** As a visitor, I want the site to feel like the portfolio of a Gen AI, Agentic AI, and Data specialist, so that the owner's focus is immediately clear.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL retain the existing dark theme, the `--accent #ff8a00` color, and the existing animation set (custom cursor, canvas particle background, typewriter hero, marquees).
2. THE Portfolio_Site SHALL present visual or textual identity cues that communicate a Gen AI, Agentic AI, and Data focus in prominent areas of the page.
3. WHEN the Visual_Identity is elevated, THE Portfolio_Site SHALL keep all existing Sections legible against the dark background at the site's defined text and accent colors.

### Requirement 13: Single Static File With No Build Step

**User Story:** As the site maintainer, I want the site to stay a single static file, so that deployment stays trivial and dependency-free.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL remain deliverable as a single `index.html` file that renders correctly when opened directly in a browser without any build or compile step.
2. THE Portfolio_Site SHALL load its runtime dependencies (fonts, icon CDNs, EmailJS) via external references without introducing a local package-install or bundling step.

### Requirement 14: Preserve Contact Form Functionality

**User Story:** As a visitor, I want the contact form to send messages, so that I can reach the owner.

#### Acceptance Criteria

1. WHEN a visitor submits the Contact_Form with valid input, THE Contact_Form SHALL send the message via EmailJS using the configured EmailJS_Config.
2. WHEN an EmailJS send succeeds, THE Portfolio_Site SHALL display a success confirmation to the visitor.
3. IF an EmailJS send fails, THEN THE Portfolio_Site SHALL display an error message directing the visitor to an alternative contact method.
4. THE Portfolio_Site SHALL preserve the existing Contact_Form fields and submission behavior after the enhancements are applied.

### Requirement 15: Address Committed EmailJS Credentials

**User Story:** As the repository owner, I want the plaintext EmailJS keys concern addressed, so that I understand the exposure and how it is handled.

#### Acceptance Criteria

1. THE Portfolio_Site SHALL isolate the EmailJS_Config values in a single clearly labeled configuration location in the script.
2. THE Portfolio_Site SHALL include a documented maintainer note describing that EmailJS public credentials are visible to any site visitor and stating the recommended mitigation (EmailJS domain allow-list restriction).
3. WHERE the EmailJS_Config is unconfigured or set to placeholder values, THE Contact_Form SHALL fall back to a defined demo-mode behavior instead of attempting a live send.
