# [4.10] Hosting Options - Manual Hosting

## Lesson Overview
![Title](./assets/images/infographic-4.10-hosting-options.png)

## Dependencies
- [Self Studies](./studies.md)
- [Lesson](./lesson.md)
- [Assignment](./assignment.md)
- Deployment guides: [Render](./render.md) · [Railway](./railway.md)

## Lesson Objectives
* **Identify** different hosting options for both codebase and database
* **Compare** and contrast different hosting platforms
* **Select** the appropriate hosting platform based on project requirements
* **Distinguish** between PaaS, IaaS, and container hosting
* **Deploy** a containerized application and a PostgreSQL database to a cloud platform (such as Render or Railway), starting from a Docker Hub image

## Lesson Plan

| Duration | What | How or Why |
|----------|------|------------|
| 10 min | Warm up | Intro and lesson overview, recap containerization and Docker Hub |
| 20 min | Part 1: Understanding application hosting | What hosting is, types of hosting — PaaS, IaaS, container hosting |
| 30 min | Part 2: Comparing hosting platforms | Railway, Render, Fly.io, Heroku — pros, cons, free tiers, comparison table |
| 10 min | Activity 1 — Platform matching discussion | Learners match platforms to scenarios based on Part 2 comparisons |
| 35 min | Part 3: Deploy your Docker image to a cloud platform | Learners build and push devops-demo `v4.10.0` (no database) to Docker Hub, deploy it from the image to the class platform (Render or Railway, following `render.md` / `railway.md`), get a public URL and test `/hello` |
| 25 min | Part 4: Hosting databases (hands-on) | Database hosting options; learners push devops-demo `v4.10.1` (database config), create PostgreSQL on the same platform, connect it with `SPRING_DATASOURCE_*` environment variables, switch the service to `v4.10.1` and check the logs |
| 25 min | Part 5: Platform selection activity | Small groups work through 4 hosting scenario cards and justify platform choices |
| 10 min | Part 6: Deployment best practices | Environment variables, health checks, logging, resource limits, common issues |
| 10 min | Recap and wrap up | Key takeaways, preview Lesson 4.12 (automated deployment), Q&A |
| **Total** | | **175 min — allows ~5 min buffer** |