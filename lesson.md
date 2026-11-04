# Lesson 4.10: Hosting Options - Manual Hosting

## Learning Objectives

By the end of this lesson, you will be able to:
1. **Identify** different hosting options for both codebase and database
2. **Compare** and contrast different hosting platforms
3. **Select** the appropriate hosting platform based on project requirements
4. **Distinguish** between PaaS, IaaS, and container hosting
5. **Deploy** a containerized application and a PostgreSQL database to a cloud platform (such as Render or Railway), starting from a Docker Hub image

---

## Prerequisites

Before starting this lesson, you should have:
- Completed Lesson 4.9 (CI/CD Tools)
- Understanding of Docker and containerization
- Familiarity with Docker Hub (pushing and pulling images)
- Basic knowledge of CI/CD pipelines
- Completed Lesson 4.6 (Docker Compose) - today you deploy that version of devops-demo with PostgreSQL
- Docker running on your computer and a Docker Hub account

---

## Introduction

**Key Questions:**
- Where can you host your application?
- What are the different types of hosting platforms?
- How do you choose the right hosting platform?
- What about databases?

In this lesson, we'll explore different hosting options and you'll deploy your **devops-demo** application and a PostgreSQL database to a cloud platform yourself, starting from a Docker Hub image. The lesson works with any platform that can run a Docker image - step-by-step guides are provided for [Render](./render.md) and [Railway](./railway.md). In Lesson 4.12, you'll automate the deployment from your CI pipeline.

---

## Part 1 - Understanding Application Hosting

### What is Hosting?

**Hosting** means running your application on a server that's accessible over the internet.

**What you need to host:**
1. **Your application code** (the backend/frontend)
2. **Your database** (PostgreSQL, MySQL, etc.)
3. **Static files** (images, CSS, JS - if any)

### Types of Hosting

#### 1. Platform as a Service (PaaS)

**What it is:**
- You provide the code/Docker image
- Platform handles infrastructure, scaling, monitoring
- Easiest but less control

**Examples:**
- Railway, Render, Heroku

**Best for:**
- Small to medium applications
- Quick deployment
- Teams wanting minimal DevOps work

---

#### 2. Infrastructure as a Service (IaaS)

**What it is:**
- You get virtual machines (VMs)
- You install everything yourself (OS, Docker, database, etc.)
- Most control but most work

**Examples:**
- AWS EC2, Google Compute Engine, Azure VMs

**Best for:**
- Large enterprises
- Custom infrastructure needs
- Teams with DevOps expertise

---

#### 3. Container Hosting

**What it is:**
- Specialized for Docker containers
- You provide Docker image
- Platform runs containers at scale

**Examples:**
- Fly.io, Google Cloud Run, AWS ECS

**Best for:**
- Containerized applications
- Microservices
- Applications needing auto-scaling

---

### Hosting Your Code vs Database

**Option 1: All-in-One Platform**
- Host code AND database on same platform
- Example: Render or Railway (app + PostgreSQL database)
- ✅ Simple, one platform to manage
- ❌ Vendor lock-in

**Option 2: Separate Platforms**
- Host code on one platform (e.g. Render or Railway)
- Host database on another (AWS RDS, Supabase)
- ✅ Best tool for each job
- ❌ More complex to manage

**Option 3: Self-Hosted Database**
- Host code on cloud platform
- Run database on your own server
- ✅ Full control over data
- ❌ You manage backups, security, scaling

**For this course:** We'll use an all-in-one platform (Render or Railway) for simplicity.

---

## Part 2 - Comparing Hosting Platforms

Let's compare four popular hosting platforms for containerized applications:

### Platform Overview

| Platform | Type | Best For | Free Tier | Database Included | Difficulty |
|----------|------|----------|-----------|-------------------|------------|
| **Railway** | PaaS | Rapid prototyping | $5 one-time trial credit (30 days), then $1 free credit/month ongoing | PostgreSQL (paid) | Easy |
| **Render** | PaaS | Web apps, APIs | 750 hours/month | PostgreSQL (free, 30 days) | Easy |
| **Fly.io** | Container | Edge computing | No free tier for new accounts (removed 2024) | PostgreSQL (paid) | Medium |
| **Heroku** | PaaS | Legacy/established apps | None (paid only) | PostgreSQL (paid) | Easy |

---

### Detailed Comparison

#### 1. Railway

**✅ Pros:**
- Extremely easy to use - simplest deployment experience
- Deploy from GitHub or Docker Hub with one click
- Built-in database templates
- Beautiful dashboard and real-time logs
- Fast deployments (typically under 2 minutes)
- New accounts get a one-time $5 trial credit, no credit card required to start
- No cold starts (always-on)
- Great for learning and prototyping

**❌ Cons:**
- The $5 trial credit is one-time (valid 30 days or until spent), not a recurring monthly amount
- After the trial, the ongoing Free plan only includes $1 of credit per month
- More expensive than Render if your project outgrows the Free plan's limits (1 replica, 0.5GB RAM)
- Trial database/volume data is deleted 30 days after the trial credit expires unless you upgrade

**Free Tier Details:**
- **Trial (new accounts):** One-time $5 credit, valid for 30 days or until spent, whichever comes first. No credit card required to start.
- **After the Trial:** Railway automatically moves your account to the ongoing **Free plan** — $0/month subscription with $1 of free credit per month (does not roll over)
- **Database:** Counts against your Trial/Free plan credit
- **App hosting:** Counts against your Trial/Free plan credit
- **Note:** Great for learning — the Trial comfortably covers your first 1-2 weeks of experimentation, and the Free plan afterward keeps a small always-on app running at no ongoing cost, just with tighter resource limits (1 replica, 0.5GB RAM, 1 vCPU)

**Best Use Cases:**
- Student projects and learning (our use case!)
- Quick prototypes
- Hackathon projects
- Short-term demos
- Testing deployment workflows

**Pricing:**
- Trial: Free (one-time $5 credit, 30 days)
- Free plan (ongoing, automatic after trial): $0/month, $1 credit/month included
- Hobby plan (optional upgrade for more resources): $5/month minimum subscription, includes $5 usage credit
- Pro plan: $20/month with additional features

---

#### 2. Render

**✅ Pros:**
- Generous free tier (750 hours/month for web services)
- Free PostgreSQL database included
- Easy Docker deployment
- Auto-deploy from GitHub/GitLab
- Free SSL certificates
- Good documentation
- No credit card required for free tier

**❌ Cons:**
- Free tier apps "sleep" after 15 minutes of inactivity
- Cold starts take 30-60 seconds
- Limited to 512MB RAM on free tier
- Slower than paid tiers
- Port configuration can be tricky for beginners

**Free Tier Details:**
- **Web Service:** 750 hours/month
- **PostgreSQL:** Free for 30 days, then a 14-day grace period before the database and its data are deleted (changed from 90 days in May 2024)
- **Bandwidth:** 100GB/month
- **Builds:** Unlimited

**Best Use Cases:**
- Long-term student projects
- Small web applications
- APIs with moderate traffic
- Apps that can tolerate cold starts

**Pricing:**
- Starter: $7/month (always-on, no cold starts)
- Standard: $25/month (more resources)

---

#### 3. Fly.io

**✅ Pros:**
- Apps run close to users (edge computing)
- Fast performance globally
- Good for microservices
- Powerful CLI tool
- True always-on (no sleeping)

**❌ Cons:**
- Steeper learning curve
- Configuration more complex
- Database costs extra ($2/month minimum)
- Requires credit card
- Less beginner-friendly
- **Fly.io removed its free tier for new signups in 2024** — new accounts get only a short trial before billing starts; the free VMs described below now apply only to legacy accounts created before the change

**Free Tier Details (legacy accounts only — see note above):**
- **VMs:** Up to 3 shared-cpu-1x (256MB RAM each)
- **Bandwidth:** 160GB outbound
- **Storage:** 3GB persistent volume
- **Database:** NOT included (costs extra)

**Best Use Cases:**
- Global applications
- Microservices architecture
- Apps needing low latency
- Teams comfortable with CLI

**Pricing:**
- Database: $2-10/month
- Additional VMs: $2-7/month

---

#### 4. Heroku (Reference Only - No Free Tier)

**✅ Pros:**
- Most mature PaaS (15+ years)
- Huge ecosystem of add-ons
- Excellent documentation
- Very reliable
- Used by many companies

**❌ Cons:**
- **No free tier** (removed in 2022)
- Most expensive option
- Basic plan: $5-7/month per app
- Database: $5+/month

**Why mention it?**
- You'll see it in many tutorials and job descriptions
- Good to know it exists
- Understanding alternatives is important

**Pricing:**
- Basic App: $7/month
- PostgreSQL: $5/month (mini plan)
- Total: ~$12/month minimum

**Many developers moved to Railway and Render when Heroku removed free tier!**

---

### Quick Comparison Table

| Feature | Railway | Render | Fly.io | Heroku |
|---------|----------|--------------|---------|--------------|
| **Setup Time** | 2 minutes | 10 minutes | 15 minutes | 10 minutes |
| **Free Database** | No (paid) | Yes (PostgreSQL) | No | No |
| **Credit Card Required** | No | No | Yes | Yes |
| **Cold Starts** | No | Yes (free tier) | No | N/A |
| **Always-On (Free)** | Yes (Trial, then ongoing Free plan) | No | N/A (no free tier for new accounts) | No free tier |
| **Best For** | Learning, prototypes | Small apps | Production apps | Enterprise |
| **Learning Curve** | Very Easy | Easy | Medium | Easy |
| **Monthly Cost (Free)** | $0 (Trial 30 days, then $1 credit/mo ongoing) | $0 | N/A (no free tier for new signups) | N/A |

---

### 🏃 Activity 1 — Platform Matching Discussion (10 min)

Based on what you've just read, discuss the following with a partner:

1. A solo developer wants to deploy a Spring Boot app over a weekend to show a client. They have no budget. Which platform would you recommend and why?
2. A startup has 5,000 users across Asia and Europe and needs fast response times globally. Which platform makes the most sense?
3. Looking at the comparison table — why do you think Heroku lost so many users when they removed their free tier in 2022?

> **Key takeaway:** Free tiers are great for learning but always understand the limitations before committing to a platform.

---

## Part 3 - Deploy Your Docker Image to a Cloud Platform

In this part, **you** will deploy your own **devops-demo** application to a cloud platform from a Docker image on Docker Hub.

```
Your computer               Docker Hub                            Cloud platform
docker build + push  ───►  YOUR_USERNAME/devops-demo:v4.10.0  ───►  devops-demo service  ───►  Public URL
```

Every PaaS does this in the same way: it **pulls your image** from a registry, **runs it as a container**, and gives it a **public URL**. Only the buttons are different. Your instructor will tell you which platform the class is using. The steps below explain **what** you do and **why**; the guide for your platform shows you **where to click**:

| Platform | Step-by-step guide |
|---|---|
| **Render** | [render.md](./render.md) - Part A |
| **Railway** | [railway.md](./railway.md) - Part A |

<!-- INSTRUCTOR: tell the class which platform to use. Render is the current default. To support another platform, add a <platform>.md guide with the same Part A / Part B structure and list it in the tables in Parts 3 and 4. -->

---

### Step 1: Build and Push the devops-demo Image

We'll use devops-demo tag `v4.10.0` - the same code you used in **Lesson 4.6**, before the database was added. It has a **multi-stage Dockerfile** that builds the JAR inside Docker, so you don't need `mvn` on your computer.

For Windows users, use the WSL terminal only. For macOS users, use your preferred terminal.

**1.1: Download the project**

```sh
cd ~
cd playground
curl -L -O https://github.com/aie-devops/devops-demo/archive/refs/tags/v4.10.0.zip
unzip v4.10.0.zip
cd devops-demo-4.10.0
```

**1.2: Build and push the image** (replace `YOUR_DOCKERHUB_USERNAME`)

```sh
docker login
docker build --platform linux/amd64 -t YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0 .
docker push YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0
```

The build takes a few minutes the first time while Maven downloads dependencies.

> **Why `--platform linux/amd64`?** Cloud platforms such as Render and Railway run on `linux/amd64` servers. If you build on an Apple Silicon Mac (M1/M2/M3/M4) without this flag, the image is built for ARM and will crash on the platform with `exec format error`. On Apple Silicon, this build is slower because Docker emulates `amd64`.

> **Why the tag `v4.10.0` and not `latest`?** Your `latest` tag holds the image your CI pipeline published in Lesson 4.7 / 4.9, which is a different version of devops-demo. Tagging each image with its version (the same as the Git tag) keeps them apart, and lets you choose exactly which version the platform runs - you'll switch versions in Part 4.

**1.3: Check Docker Hub**

1. Go to [https://hub.docker.com](https://hub.docker.com) and open your `devops-demo` repository
2. Check that the `v4.10.0` tag is there
3. Check that the repository is **Public** (platforms can't pull private images without extra setup)

<details>
<summary>Build or push not working? Use the instructor's image instead.</summary>

If you can't build or push your own image in time, use the instructor's public image in Step 3:

```
jsstrn/devops-demo:v4.10.0
```

It is built from the same `v4.10.0` code, for both `linux/amd64` and `arm64`.

</details>

---

### Step 2: Create an Account on the Platform

Sign up for the platform your class is using. Signing up with **GitHub** is the quickest. Keep this account - you can use it again in later lessons and for your module assignment.

➡️ **Render:** [render.md - Step A1](./render.md#step-a1-create-a-render-account) · **Railway:** [railway.md - Step A1](./railway.md#step-a1-create-a-railway-account)

---

### Step 3: Create a Service from Your Docker Image

Create a new **service** (a running application) and point it at your image:

```
YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0
```

Spring Boot listens on port **8080**, so make sure the platform sends web traffic to port `8080`. Each platform asks for this in a slightly different place - your guide shows where.

➡️ **Render:** [render.md - Step A2](./render.md#step-a2-create-a-web-service-from-your-docker-image) · **Railway:** [railway.md - Steps A2-A3](./railway.md#step-a2-create-a-service-from-your-docker-image)

---

### Step 4: Watch the Deployment Logs

The platform pulls your image from Docker Hub and starts the container. Open the service's **logs** and watch Spring Boot start:

```
 :: Spring Boot ::

Tomcat started on port 8080 (http) with context path '/'
Started DevopsDemoApplication in 2.9 seconds
```

These are the same logs you saw with `docker logs` on your computer - the platform just shows them in the browser. A deploy usually takes 1-3 minutes.

---

### Step 5: Test Your Application

1. Copy your service's **public URL** from the platform's dashboard
2. Add `/hello` to the end of the URL and open it in your browser, or use curl:
   ```bash
   curl https://YOUR-SERVICE-URL/hello
   ```
3. You should see:
   ```
   DevOps demo - now on Docker Hub!
   ```

**Success!** Your devops-demo application is running in the cloud. ✅

---

### Step 6: Explore the Platform's Features

Take a few minutes to explore your service. Most platforms have the same building blocks:

| Feature | What it's for |
|---|---|
| **Deployments / Events** | History of every deploy, each with its own logs; redeploy or roll back |
| **Logs** | Everything your container prints |
| **Variables / Environment** | Environment variables for database URLs, API keys and other settings (you'll use this in Part 4) |
| **Metrics** | CPU, memory and network usage |
| **Settings** | The source image, networking (domain), region and plan |

---

### Step 7: Troubleshooting

| Problem | Likely cause | What to check |
|---|---|---|
| `exec format error` in the logs | Image built for ARM (Apple Silicon) | Rebuild with `--platform linux/amd64`, push, redeploy |
| Image not found / image pull failed | Wrong name or tag, or private repository | The tag exists on Docker Hub, the name is `username/devops-demo:v4.10.0`, the repository is **Public** |
| URL doesn't respond | No public URL, or traffic sent to the wrong port | The service has a public URL and uses port `8080` |

Your platform guide has the exact fixes: [render.md - Step A6](./render.md#step-a6-troubleshooting) · [railway.md - Step A7](./railway.md#step-a7-troubleshooting)

---

## Part 4 - Hosting Databases

### Database Hosting Options

**Option 1: Same Platform as Your App**
- Render, Railway and similar platforms can host PostgreSQL, MySQL, MongoDB and Redis next to your app
- ✅ Simple - one dashboard, one project
- ✅ Fast, private networking between app and database

**Option 2: Specialized Database Services**
- Managed database providers, e.g. **Supabase** (PostgreSQL), **MongoDB Atlas** (MongoDB)
- ✅ Database is managed separately from where the app runs
- ❌ You manage connection strings across two providers
- ❌ Traffic goes over the public internet (slightly slower)

**Option 3: Cloud Provider Databases**
- **AWS RDS**, **Google Cloud SQL**, **Azure Database** (PostgreSQL, MySQL)
- ✅ Production-grade, highly scalable
- ❌ More complex to set up and run

---

### Hands-On: Add PostgreSQL to Your Deployment

Now you'll give devops-demo a database. You will:
1. Build and push devops-demo `v4.10.1` - the version that adds the database configuration
2. Create a PostgreSQL database on the same platform
3. Point your app at the database with environment variables, and update it to the `v4.10.1` image

```
Cloud platform
┌──────────────────────────────────────────────────────────┐
│                                                          │
│   devops-demo  ───── private network ─────►  PostgreSQL  │
│   (devops-demo:v4.10.1)                      (database)  │
│                                                          │
└──────────────────────────────────────────────────────────┘
        ▲
   Public URL (/hello)
```

| Platform | Step-by-step guide |
|---|---|
| **Render** | [render.md](./render.md) - Part B |
| **Railway** | [railway.md](./railway.md) - Part B |

---

### Step 1: Build and Push devops-demo v4.10.1

Tag `v4.10.1` is the next step from Lesson 4.6: it adds Spring Data JPA, the PostgreSQL driver, and these database settings in `application.properties`:

```properties
spring.datasource.url=${SPRING_DATASOURCE_URL}
spring.datasource.username=${SPRING_DATASOURCE_USERNAME}
spring.datasource.password=${SPRING_DATASOURCE_PASSWORD}
```

The app reads its database settings from **environment variables** - in Lesson 4.6, Docker Compose set them. Today, the platform will.

Build and push it to your Docker Hub repository, as in Part 3:

```sh
cd ~/playground
curl -L -O https://github.com/aie-devops/devops-demo/archive/refs/tags/v4.10.1.zip
unzip v4.10.1.zip
cd devops-demo-4.10.1

docker build --platform linux/amd64 -t YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1 .
docker push YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1
```

> **Don't deploy this image yet.** Without the database variables, this version can't start - you'll switch to it in Step 5.

<details>
<summary>Build or push not working? Use the instructor's image instead.</summary>

Skip the build and use the instructor's public image, built from the same `v4.10.1` code:

```
jsstrn/devops-demo:v4.10.1
```

</details>

---

### Step 2: Create a PostgreSQL Database

Create a **PostgreSQL** database on the same platform, next to your devops-demo service. Put it in the **same project / region** as the app, so they can talk over the platform's private network.

➡️ **Render:** [render.md - Step B1](./render.md#step-b1-create-a-postgresql-database) · **Railway:** [railway.md - Step B1](./railway.md#step-b1-add-a-postgresql-database)

---

### Step 3: Find the Connection Details

The platform generates the database's connection details for you:

| Detail | What it is |
|---|---|
| **Host** | The database's private hostname on the platform's network |
| **Port** | `5432` (the PostgreSQL default) |
| **Database** | The database name |
| **Username / Password** | Generated by the platform |
| **Database URL** | All of the above in one `postgresql://user:password@host:port/database` URL |

> **Important:** the platform's `postgresql://...` URL is **not** in the format Spring Boot needs. Spring Boot needs a **JDBC URL** that starts with `jdbc:postgresql://` and has no username or password in it:
>
> ```
> jdbc:postgresql://<host>:<port>/<database>
> ```

➡️ **Render:** [render.md - Step B2](./render.md#step-b2-find-the-connection-details) · **Railway:** [railway.md - Step B2](./railway.md#step-b2-look-at-the-database-variables)

---

### Step 4: Connect devops-demo to the Database

Give devops-demo the same three environment variables Docker Compose gave it in Lesson 4.6 - this time pointing at the platform's database. Add them to the **devops-demo** service (not the database):

| Variable | Value |
|---|---|
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://<host>:<port>/<database>` |
| `SPRING_DATASOURCE_USERNAME` | the database username |
| `SPRING_DATASOURCE_PASSWORD` | the database password |

**Compare with Lesson 4.6:**

| | Docker Compose (Lesson 4.6) | Cloud platform (today) |
|---|---|---|
| Database host | `db` (service name) | The database's private hostname |
| Where variables live | `environment:` in `compose.yaml` | The service's **Variables / Environment** page |
| Password | Written in `compose.yaml` | Generated by the platform |

➡️ **Render:** [render.md - Step B3](./render.md#step-b3-connect-devops-demo-to-the-database) · **Railway:** [railway.md - Step B3](./railway.md#step-b3-connect-devops-demo-to-the-database)

---

### Step 5: Update to v4.10.1 and Deploy

Point the devops-demo service at the new image tag, `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1` (or `jsstrn/devops-demo:v4.10.1`), and deploy it together with the new variables.

➡️ **Render:** [render.md - Step B4](./render.md#step-b4-update-to-v4101-and-deploy) · **Railway:** [railway.md - Step B4](./railway.md#step-b4-update-to-v4101-and-deploy)

This time the logs should show the app connecting to the database before it starts:

```
HikariPool-1 - Starting...
HikariPool-1 - Added connection org.postgresql.jdbc.PgConnection@...
HikariPool-1 - Start completed.
Tomcat started on port 8080 (http) with context path '/'
Started DevopsDemoApplication in 6.1 seconds
```

`HikariPool-1 - Start completed` means Spring Boot connected to your PostgreSQL database in the cloud. ✅

---

### Step 6: Test Your Application Again

```bash
curl https://YOUR-SERVICE-URL/hello
```

You should see:
```
DevOps demo - now on Docker Hub!
```

The message is the same as in Part 3 - the difference is in the logs: your app is now connected to a database.

**Success!** Your devops-demo app and its PostgreSQL database are both running in the cloud. 🎉

---

### Step 7: Troubleshooting

| Error in the logs | Likely cause | What to check |
|---|---|---|
| `'url' must start with "jdbc"` | `SPRING_DATASOURCE_URL` is the `postgresql://...` URL | Use `jdbc:postgresql://<host>:<port>/<database>` |
| `UnknownHostException` or "Connection refused" | The app can't reach the database's private hostname | App and database are in the same project / region; the database is running; the host has no typos |
| `password authentication failed` | Wrong username or password | Copy them again - no extra spaces or quotes |
| `Could not resolve placeholder 'SPRING_DATASOURCE_URL'` | Variables missing, or added to the database instead of the app | Add them to the **devops-demo** service, then deploy |

Your platform guide has the exact fixes: [render.md - Step B6](./render.md#step-b6-troubleshooting) · [railway.md - Step B6](./railway.md#step-b6-troubleshooting)

---

### Clean Up (Optional)

Running services use your free allowance, and free databases may expire (Render's free PostgreSQL is deleted after 30 days plus a grace period). Once you've finished testing, delete the database and the service - see the **Clean Up** section of your platform guide.

---

## Part 5 - Hands-On Activity: Platform Selection

**Format:** Small groups (3-4 students)

### Instructions

You will receive 4 hosting scenario cards. For each scenario:
1. Read the requirements
2. Discuss which hosting platform fits best
3. Justify your choice with 2-3 reasons

---

### Scenario 1: Student Portfolio Project

**Project:** Personal portfolio website with blog
**Tech Stack:** React frontend, Spring Boot backend, PostgreSQL
**Requirements:**
- Learning project (not production)
- Budget: $0
- Need both app and database
- Okay with occasional downtime
- Want to learn deployment

**Question:** Which platform should you use?

<details>
<summary>Suggested Answer</summary>

**Best Choice:** Railway

**Reasons:**
1. Extremely simple deployment - great for learning
2. The one-time $5 trial credit covers the initial learning period (5-10 days), and the ongoing Free plan afterward keeps a small app running at no cost
3. No cold starts - always responsive
4. Beautiful dashboard for monitoring
5. Easy database integration
6. Automatically continues on the Free plan after the trial - no manual restart needed

**Alternative:** Render (if you need a database that stays free for longer than 30 days without a grace-period upgrade)

</details>

---

### Scenario 2: Weekend Hackathon

**Project:** AI chatbot for event
**Tech Stack:** Python backend, Redis cache, PostgreSQL
**Requirements:**
- Need to deploy in 2 days
- Show to judges (one weekend)
- Budget: $10
- Speed of deployment more important than cost
- Needs to be online 48 hours only

**Question:** Which platform should you use?

<details>
<summary>Suggested Answer</summary>

**Best Choice:** Railway

**Reasons:**
1. Fastest deployment - literally 2 minutes from Docker image
2. The one-time $5 trial credit is more than enough for a 48-hour hackathon
3. Easy to add Redis and PostgreSQL templates
4. No cold starts during demo
5. Clean, impressive dashboard to show judges
6. Can delete after hackathon

</details>

---

### Scenario 3: Side Project Going Viral

**Project:** URL shortener app
**Tech Stack:** Node.js backend, Redis, PostgreSQL
**Requirements:**
- Started as hobby, now has 10,000 users
- Users in USA, Europe, Asia
- Need fast response times globally
- Budget: $20-30/month
- Can handle some DevOps complexity

**Question:** Which platform should you use?

<details>
<summary>Suggested Answer</summary>

**Best Choice:** Fly.io

**Reasons:**
1. Edge computing = fast globally
2. Can deploy to multiple regions
3. No cold starts
4. $20-30/month budget covers app + database (note: Fly.io no longer offers a free tier for new accounts, so this budget goes entirely toward paid usage from day one)
5. Better performance for viral traffic
6. Scales well as users grow

**Why not Railway or Render:** each service runs in one region you pick, so users far from that region get slower responses. Fly.io can run copies of the app in many regions

</details>

---

### Scenario 4: Corporate Application

**Project:** Internal employee management system
**Tech Stack:** Java Spring Boot, PostgreSQL, Redis
**Requirements:**
- 500 employees using daily
- Must be reliable (no downtime)
- Company has IT budget ($200/month)
- Needs to integrate with existing systems
- Security and compliance important
- Team has DevOps engineer

**Question:** Which platform should you use?

<details>
<summary>Suggested Answer</summary>

**Best Choice:** AWS (IaaS) or Heroku

**Reasons:**
1. Budget allows enterprise solutions
2. AWS: Full control for security/compliance
3. Heroku: Reliable, mature, many add-ons
4. Free tiers not suitable for 500 users
5. Need production-grade reliability
6. Company can afford best solution

**Why not Railway/Render:** Not designed for enterprise-scale applications with strict compliance needs

</details>

---

## Part 6 - Deployment Best Practices

### Before Deploying to Production

**1. Environment Variables:**
- Never hardcode secrets in code
- Use environment variables for:
  - Database URLs
  - API keys
  - Secret tokens
- Example: `DATABASE_URL`, `JWT_SECRET`

**2. Health Checks:**
- Add a `/health` endpoint
- Returns 200 OK if app is healthy
- Platforms use this to verify app is running

**3. Logging:**
- Log to stdout/stderr (not files)
- Platforms capture these logs
- Makes debugging easier

**4. Resource Limits:**
- Understand platform limits (RAM, CPU)
- Monitor usage in dashboard
- Optimize app if needed

**5. Database Migrations:**
- Plan how to run migrations
- Some platforms auto-run on deploy
- Others need manual trigger

---

### Common Deployment Issues

**Issue 1: App doesn't start**
- Check logs for errors
- Verify environment variables are set
- Ensure the platform sends traffic to the port your app listens on (8080 for Spring Boot)

**Issue 2: Database connection fails**
- Verify the JDBC URL (`SPRING_DATASOURCE_URL`) starts with `jdbc:postgresql://`
- Check that database and app are in same project
- Ensure database is running

**Issue 3: Image keeps rebuilding**
- Check which image tag the service is set to
- Some platforms don't notice a new image on Docker Hub until you redeploy
- Use versioned tags so you know exactly what is running

**Issue 4: App runs locally but not on platform**
- Check Java/Node version compatibility
- Verify all dependencies are in pom.xml/package.json
- Review build logs for errors