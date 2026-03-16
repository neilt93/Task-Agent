---
tags: jobs
target: 50
---

## This Week

```dataview
TABLE WITHOUT ID
  length(filter(file.tasks, (t) => t.completed)) + " / " + this.target AS "Applied / Target",
  this.target - length(filter(file.tasks, (t) => t.completed)) AS "Remaining"
WHERE file.name = "Jobs"
```

> Log each application as a task. Check it off once submitted.

### Applied

- [x] Scale AI - ML Research Engineer, Agent Data Foundation *(2026-03-03)* [link](https://job-boards.greenhouse.io/scaleai/jobs/4625345005)
- [x] Stability AI - Generative AI Inference Engineer *(2026-03-02)* [link](http://stability.ai/careers?gh_jid=4712826101)
- [x] DeepMind - Research Engineer, Multimodal Generative AI *(2026-03-02)* [link](https://job-boards.greenhouse.io/deepmind/jobs/7339604)
- [x] Mistral AI - Research Engineer, Machine Learning *(2026-03-02)* [link](https://jobs.lever.co/mistral/bada0014-0f32-4370-b55f-81c5595c7339)
- [x] Vercel - Software Engineer, AI SDK *(2026-03-02)* [link](https://job-boards.greenhouse.io/vercel/jobs/5474915004)
- [x] Stripe - PhD ML Engineer, New Grad *(2026-03-02)* [link](https://stripe.com/jobs/search?gh_jid=7216668)
- [x] Figma - Software Engineer, AI Product *(2026-03-02)* [link](https://boards.greenhouse.io/figma/jobs/5551730004)
- [x] Runway - Director of ML, Datasets Engineering *(2026-03-02)* [link](https://job-boards.greenhouse.io/runwayml/jobs/4483573005)
- [x] Scale AI - ML Research Engineer, Agents *(2026-03-02)* [link](https://job-boards.greenhouse.io/scaleai/jobs/4625344005)
- [x] Databricks - Sr. ML Engineer *(2026-03-02)* [link](https://databricks.com/company/careers/open-positions/job?gh_jid=7276195002)
- [x] Anthropic - Training Content and Systems Architect *(2026-03-02)* [link](https://job-boards.greenhouse.io/anthropic/jobs/5097351008)

### Active Conversations

- [ ] Baton Corporation (pump.fun) - Interview with Lloyd McCarthy, scheduled 2026-03-26 2pm ET
- [ ] PermitFlow - SWE New Grad, $54M Series B (YC), replied to Sam Lam (CTO) 2026-03-15
- [ ] Eigen Labs - Summer Fellowship 2026, interview 2026-03-12
- [ ] Hyde - Intro from Karan Sirdesai to Aditya Rajan (founding team), connected on WhatsApp 2026-03-13
- [ ] The Residency - Nick Linck, meeting 2026-03-17 2pm ET
- [ ] Fracta Labs - Naman Kapasi, meeting 2026-03-17 3:30pm ET
- [ ] Anthropic Fellows / Constellation - Research Brainstorm call 2026-03-11

### Pipeline

- [ ] Scale AI - Software Engineer, New Grad (50.7)
- [ ] DeepMind - SWE, ML and EDA GenAI (51.2)
- [ ] Figma - Software Engineer, ML (50.2)
- [ ] Stripe - ML Engineer, Payments ML Accelerator (50.2)
- [ ] Stripe - ML Engineer, Stripe Assistant (50.2)
- [ ] Stripe - SWE, ML Infrastructure (50.2)
- [ ] Anthropic - Applied AI Engineer, Beneficial Deployments (50.0)
- [ ] Anthropic - Applied AI Engineer, Digital Natives (50.0)
- [ ] Anthropic - Applied AI Engineer, Startups (50.0)
- [ ] Anthropic - Forward Deployed Engineer, Applied AI (50.0)
- [ ] Anthropic - SWE, Claude Code (50.0)
- [ ] Anthropic - SWE, Agent SDK - Claude Code (50.0)
- [ ] Anthropic - Full Stack SWE, Reinforcement Learning (50.0)
- [ ] Mistral AI - Applied AI, Forward Deployed MLE (51.2)

### Rejected

- MIT EECS PhD *(2026-03-10)*
