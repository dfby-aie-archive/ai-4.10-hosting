# Deploying devops-demo to Railway

Step-by-step instructions for **Lesson 4.10, Parts 3 and 4** on [Railway](https://railway.app). Do the matching step in [lesson.md](./lesson.md) first (build and push your image), then come here for the clicks.

- **Part A** - Deploy devops-demo `v4.10.0` from Docker Hub (lesson.md Part 3)
- **Part B** - Add a PostgreSQL database and update to `v4.10.1` (lesson.md Part 4)

> Using Render instead? Follow [render.md](./render.md).

---

## Before You Start

- Your image `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0` is on Docker Hub (lesson.md Part 3, Step 1)
- The Docker Hub repository is **Public** - Railway can't pull private images without extra setup
- The image was built with `--platform linux/amd64` - Railway runs on `linux/amd64` servers

**Railway's free usage (check the current details on [railway.com/pricing](https://railway.com/pricing)):** new accounts get a one-time trial credit, then move to the ongoing Free plan with a small monthly credit. The app and the database both use this credit.

---

## Part A - Deploy devops-demo from Docker Hub

### Step A1: Create a Railway Account

1. Go to [https://railway.app](https://railway.app)
2. Click **"Login"** or **"Start a New Project"**
3. Sign up with **GitHub** (recommended)
4. Verify your email if prompted

**Dashboard Overview:**
- **Projects:** Collections of related services
- **Services:** Individual applications or databases
- **Deployment logs:** Shown in real time

> Keep this account - you can use it again in later lessons and for your module assignment.

### Step A2: Create a Service from Your Docker Image

1. Click **"Start a New Project"** (or "New Project")
2. Select **"Deploy from Docker Image"**
3. In the dialog, enter your image name:
   ```
   YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0
   ```
   (No `docker.io/` prefix needed)
4. Click **"Deploy"**

Railway pulls your image from Docker Hub and starts the container.

### Step A3: Claim the Project and Generate a Domain

**Claim the project:** if you see a warning that the project is temporary and will be deleted, click **"Claim Project"** (top right) to make it permanent.

**Generate a public domain:**

1. Click the **devops-demo** service in the project view
2. Click the **"Settings"** tab
3. Scroll down to the **"Networking"** section
4. Under **"Public Networking"**, click **"Generate Domain"**
5. In the dialog:
   - **Port:** Enter `8080` (Spring Boot default port)
   - Click **"Generate Domain"**

Railway assigns a URL like:
```
https://devops-demo-production.up.railway.app
```

### Step A4: Watch the Deployment Logs

Click the **"Deployments"** tab, then click the latest deployment to see its logs in real time:

```
==> Pulling image YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0
==> Starting container...

 :: Spring Boot ::

Tomcat started on port 8080 (http) with context path '/'
Started DevopsDemoApplication in 2.9 seconds
```

It usually takes about 1-2 minutes.

### Step A5: Test Your Application

1. Copy your Railway URL (from **Settings → Networking**)
2. Add `/hello` to the end of the URL and open it in your browser, or use curl:
   ```bash
   curl https://YOUR-RAILWAY-URL.up.railway.app/hello
   ```
3. You should see:
   ```
   DevOps demo - now on Docker Hub!
   ```

**Success!** devops-demo is running on Railway. ✅ Go back to [lesson.md](./lesson.md) Part 3.

### Step A6: Explore Railway's Features

- **Deployments:** history of every deployment, each with its own logs; redeploy or roll back
- **Variables:** environment variables for database URLs, API keys and other settings. Changing a variable triggers a new deployment
- **Metrics:** CPU, memory and network usage
- **Settings:** source image, networking (domains) and deploy settings

### Step A7: Troubleshooting

**`exec format error` in the logs**
- **Cause:** the image was built for ARM (Apple Silicon Mac) instead of `linux/amd64`
- **Solution:** rebuild with `docker build --platform linux/amd64 ...`, push again, then click **"Redeploy"** in Railway

**"Image not found" error**
- **Cause:** the image name or tag is wrong, or the image isn't on Docker Hub
- **Solution:** check the image and the `v4.10.0` tag exist on [Docker Hub](https://hub.docker.com), the name format is `username/devops-demo:v4.10.0`, the repository is **Public**, and there is no `docker.io/` prefix

**Can't access the app via the URL**
- **Cause:** the domain isn't generated, or the port is wrong
- **Solution:** go to **Settings → Networking**, check that "Public Networking" shows a domain and that the port is `8080`. Regenerate the domain if needed

---

## Part B - Add PostgreSQL and Update to v4.10.1

Do lesson.md Part 4, **Step 1** first: build and push `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1`.

> **Don't deploy `v4.10.1` yet.** Railway watches your Docker Hub repository and will soon notice the newer tag. Without the database variables, this version can't start - you'll apply the update in Step B4.

```
Railway project
┌──────────────────────────────────────────────────────────┐
│                                                          │
│   devops-demo  ───── private network ─────►  Postgres    │
│   (devops-demo:v4.10.1)                      (database)  │
│                                                          │
└──────────────────────────────────────────────────────────┘
        ▲
   Public URL (/hello)
```

### Step B1: Add a PostgreSQL Database

1. In your Railway project, click **"New"** (or **"+ Create"**)
2. Select **"Database"** → **"Add PostgreSQL"**

Railway creates a new **Postgres** service in your project. It's ready in less than a minute.

### Step B2: Look at the Database Variables

1. Click the **Postgres** service
2. Open the **"Variables"** tab

Railway created the connection details as variables, including:

| Variable | Example value |
|---|---|
| `PGHOST` | `postgres.railway.internal` |
| `PGPORT` | `5432` |
| `PGUSER` | `postgres` |
| `PGPASSWORD` | `<generated-password>` |
| `PGDATABASE` | `railway` |
| `DATABASE_URL` | `postgresql://postgres:<password>@postgres.railway.internal:5432/railway` |

> **Important:** `DATABASE_URL` is **not** in the format Spring Boot needs. Spring Boot needs a **JDBC URL** that starts with `jdbc:postgresql://` and has no username or password in it. So we build our own URL from the individual variables in the next step.

### Step B3: Connect devops-demo to the Database

1. Click the **devops-demo** service
2. Open the **"Variables"** tab
3. Click **"New Variable"** and add each of these (copy them exactly):

| Variable | Value |
|---|---|
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://${{Postgres.PGHOST}}:${{Postgres.PGPORT}}/${{Postgres.PGDATABASE}}` |
| `SPRING_DATASOURCE_USERNAME` | `${{Postgres.PGUSER}}` |
| `SPRING_DATASOURCE_PASSWORD` | `${{Postgres.PGPASSWORD}}` |

**What is `${{Postgres.PGHOST}}`?** It's a **reference variable**. Railway replaces it with the value of `PGHOST` from the service called **Postgres**. If Railway rotates the password or you rename the database, your app picks up the new values automatically. No passwords are copied by hand.

### Step B4: Update to v4.10.1 and Deploy

Railway regularly checks Docker Hub for newer versions of your image. Because you pushed `v4.10.1` to the same repository as `v4.10.0`, Railway offers you the update.

1. Click the **devops-demo** service → **"Settings"** tab
2. Under **"Source"**, look for the **update available** button for `v4.10.1` and click it. Railway stages the new image tag.
3. Railway shows a banner with your pending changes (the new variables and the new image). Click **"Deploy"** (or **"Apply changes"**)
4. Open the **"Deployments"** tab and click the new deployment

> **No update button?** Railway checks for new versions periodically, so it can take a few minutes to appear. If it doesn't, change the **Source Image** by hand to `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1` (or `jsstrn/devops-demo:v4.10.1` if you used the instructor's image - update detection only works for images in your own repository), then deploy.

The logs should show the app connecting to the database before it starts:

```
HikariPool-1 - Starting...
HikariPool-1 - Added connection org.postgresql.jdbc.PgConnection@...
HikariPool-1 - Start completed.
Tomcat started on port 8080 (http) with context path '/'
Started DevopsDemoApplication in 6.1 seconds
```

### Step B5: Test Your Application Again

```bash
curl https://YOUR-RAILWAY-URL.up.railway.app/hello
```

You should see the same message as in Part A. **Success!** devops-demo and its PostgreSQL database are both running on Railway. 🎉 Go back to [lesson.md](./lesson.md) Part 4.

### Step B6: Troubleshooting

**`'url' must start with "jdbc"`**
- **Cause:** `SPRING_DATASOURCE_URL` is set to `DATABASE_URL` (or another `postgresql://...` URL)
- **Solution:** use `jdbc:postgresql://${{Postgres.PGHOST}}:${{Postgres.PGPORT}}/${{Postgres.PGDATABASE}}`

**`UnknownHostException: postgres.railway.internal` or "Connection refused"**
- **Cause:** the app can't reach the database over the private network
- **Solution:** check the app and the database are in the **same project** (and the same environment); check the database service is called **Postgres** - the name in `${{Postgres.PGHOST}}` must match the service name exactly; check the Postgres service is running (green), then redeploy devops-demo

**`password authentication failed for user "postgres"`**
- **Cause:** the username or password variable is wrong
- **Solution:** check `SPRING_DATASOURCE_USERNAME` is `${{Postgres.PGUSER}}` and `SPRING_DATASOURCE_PASSWORD` is `${{Postgres.PGPASSWORD}}` - no extra spaces or quotes

**`Could not resolve placeholder 'SPRING_DATASOURCE_URL'`**
- **Cause:** the `v4.10.1` image is running, but the variables were added to the wrong service or weren't deployed
- **Solution:** add the variables to the **devops-demo** service (not Postgres), then deploy the changes

---

## Clean Up (Optional)

Running services use your Railway credit. Once you've finished testing, delete the project from **Project Settings → Danger → Delete Project**.
