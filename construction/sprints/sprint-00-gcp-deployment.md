# Sprint 00: GCP Deployment

**Sprint Goal:** Deploy Denario to GCP Cloud Run and verify it's operational

**Start Date:** 2025-12-18
**Status:** In Progress

---

## Sprint Backlog

### Task 1: GCP Project Setup
```bash
# List projects you have access to
gcloud projects list

# Use an existing project (recommended for org users)
gcloud config set project <existing-project-id>

# OR create new (only if you have org-level permissions)
# gcloud projects create <new-project-id>

# Verify active project
gcloud config get-value project
```
- [ ] Select existing project OR create new (if permitted)
- [ ] Verify billing is enabled on the project
- [ ] Note project ID: `__________________`

### Task 2: Enable Required APIs
```bash
gcloud services enable run.googleapis.com
gcloud services enable artifactregistry.googleapis.com
gcloud services enable aiplatform.googleapis.com
```

### Task 3: Vertex AI Service Account
- [ ] Go to IAM & Admin > Service Accounts
- [ ] Create service account named "denario"
- [ ] Assign "Vertex AI User" role
- [ ] Create JSON key
- [ ] Download and rename to `gemini.json`
- [ ] Store securely (do NOT commit to repo)

### Task 4: Build Docker Image
```bash
# From repository root
docker build -f docker/Dockerfile.prod -t denario .
```

### Task 5: Push to Container Registry
```bash
# Configure Docker for GCR
gcloud auth configure-docker

# Tag image
docker tag denario gcr.io/<PROJECT_ID>/denario:latest

# Push
docker push gcr.io/<PROJECT_ID>/denario:latest
```

### Task 6: Deploy to Cloud Run
```bash
gcloud run deploy denario \
  --image gcr.io/<PROJECT_ID>/denario:latest \
  --platform managed \
  --region us-central1 \
  --port 8501 \
  --memory 2Gi \
  --allow-unauthenticated \
  --set-env-vars "OPENAI_API_KEY=<key>,ANTHROPIC_API_KEY=<key>"
```

### Task 7: Verification
- [ ] Access Cloud Run URL in browser
- [ ] Verify Streamlit GUI loads
- [ ] Test basic Denario functionality
- [ ] Test LLM connectivity (create test project)

---

## Dependencies

- GCP account with billing enabled
- LLM API keys (OpenAI, Anthropic, or Gemini)
- Docker installed locally
- gcloud CLI installed and authenticated

---

## Risks and Blockers

| Risk | Mitigation |
|------|------------|
| GCP billing not set up | User must enable billing first |
| API keys not available | Can deploy without, test later |
| Docker build fails | Check disk space, use Dockerfile.prod |
| Cloud Run timeout | Increase timeout, check memory |

---

## Acceptance Criteria

1. Denario GUI accessible via Cloud Run URL
2. Can create a new project in the GUI
3. LLM calls succeed (at least one provider)
4. No errors in Cloud Run logs

---

## Notes

- See [techContext.md](../../memory-bank/techContext.md) for detailed deployment commands
- See [docs/llm_api_keys/vertex-ai-setup.md](../../docs/llm_api_keys/vertex-ai-setup.md) for Vertex AI setup
- For secrets, consider using GCP Secret Manager in production
