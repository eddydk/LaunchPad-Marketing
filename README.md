# LaunchPad Marketing – Multi-Agent Generative AI Solution

## 📌 Overview

LaunchPad Marketing needed a faster, more consistent way to generate **multi-channel marketing content** for tech product launches.  
The solution is a **multi-agent Generative AI system** built with **Google Cloud Vertex AI Agent Engine** and **ADK** that automates:

1. **Blog Post Agent** – Produces structured, ~500-word product announcement blog posts in Markdown.
2. **Social Media Agent** – Generates short-form, platform-specific social posts in JSON.
3. **Campaign Coordinator Agent** – Orchestrates the Blog and Social agents to deliver a **unified campaign package** (Markdown).

This system enables **faster go-to-market cycles**, **consistent messaging**, and **scalable campaign production**.

---

## 🏗 Architecture & Design Choices

### **Architecture**
```
[User / UI]
   │
   ▼
Campaign Coordinator Agent
   ├── Remote Call → Blog Post Agent
   ├── Remote Call → Social Media Agent
   ├── Remote Call → Consolidator Agent
   ▼
Unified Campaign Package (Markdown)
```

### **Technical Architecture Diagram**
![alt text](https://github.com/eddydk/LaunchPad-Marketing/blob/main/images/Architecture.png?raw=true)

### **Campaign Agent Orchestration**
![alt text](https://github.com/eddydk/LaunchPad-Marketing/blob/main/images/Campaign Agent Orchestration.png?raw=true)

### **Design Decisions**
- **Remote Orchestration**  
  The Coordinator is deployed independently on Agent Engine and calls **Blog Post & Social Media agents** via session-scoped HTTP `:stream_query()` calls.  
  This avoids bundling large models or business logic directly into one monolithic agent.

- **Flexible Output Handling**  
  The parsing functions `_extract_from_content_obj` and `_normalize_engine_query_payload` ensure the system handles text, Markdown, JSON, and base64-encoded content.

- **Session Management**  
  All remote calls explicitly create sessions via `create_session` to prevent "Session not found" errors and maintain conversation.

---

## ⚙️ Setup & Deployment

### Option 1: Import the .ipynb in a Jupyter notebook and run each cell in order (make sure to rename the environment variables to your own setup)

### Option 2: Follow the next steps:

### **Prerequisites**
- Python 3.10+
- Google Cloud Project with Vertex AI enabled
- `google-cloud-aiplatform` with Agent Engine & ADK extras
- Service account with Vertex AI Admin and Storage permissions
- `google-genai`
- `gradio`

### **Install Dependencies**
```bash
pip install google-cloud-aiplatform[agent_engines,adk] google-auth requests
```

### **Set Environment Variables**
```bash
export GOOGLE_CLOUD_PROJECT="your-project-id"
export GOOGLE_CLOUD_LOCATION="us-central1"
export GOOGLE_CLOUD_BUCKET="your-staging-bucket"
export GEMINI_VERSION="gemini-2.0-flash" 
```

### **Deploy Agents**
In the notebook:
1. Deploy **Blog Post Agent** → `remote_blog_agent`
2. Deploy **Social Media Agent** → `remote_social_agent`
3. Deploy **Campaign Coordinator Agent** → pass sub-agent resource names via:
```python
env_vars={
  "APP_LOCATION": LOCATION,
  "BLOG_ENGINE_RESOURCE": BLOG_ENGINE_RESOURCE,
  "SOCIAL_ENGINE_RESOURCE": SOCIAL_ENGINE_RESOURCE,
}
```

### **Run Locally with Gradio**
The notebook contains a Gradio UI:
- Select Agent
- Select Prompt
- Enter and Finalze Product Brief
- Get either Markdown (Blog) or JSON (Social) output

---

## 🚀 Potential Next Steps
1. **Human-in-the-loop Review** – Allow content approval and editing before publishing.
2. **Multi-language Support** – Generate content in different languages for global launches.
3. **Analytics Feedback Loop** – Feed engagement metrics back into prompt engineering.
4. **Prompt Engineering and Enhance Output Quality** - Implement brand tone/style fine-tuning.
5. **CMS Integration** – Auto-publish to WordPress, HubSpot, or social scheduling tools.
6. **Security & Compliance** - Add brand-safety filters.

---

## 📂 Repository Structure
```
├── launchpad_marketing.ipynb      # Main development & deployment notebook
├── README.md                      # This document
├── strataprime_logo.jpg           # Logo used in GUI
```

---

**Author:** Eddy Daouk  
**Tech Stack:** Google Vertex AI Agent Engine, ADK, Gemini 2.0 Flash, Python, Gradio  
**Date:** 2025
