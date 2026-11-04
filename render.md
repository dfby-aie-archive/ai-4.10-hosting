# Deploying devops-demo to Render

Step-by-step instructions for **Lesson 4.10, Parts 3 and 4** on [Render](https://render.com). Do the matching step in [lesson.md](./lesson.md) first (build and push your image), then come here for the clicks.

- **Part A** - Deploy devops-demo `v4.10.0` from Docker Hub (lesson.md Part 3)
- **Part B** - Add a PostgreSQL database and update to `v4.10.1` (lesson.md Part 4)

> Using Railway instead? Follow [railway.md](./railway.md).

---

## Before You Start

- Your image `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0` is on Docker Hub (lesson.md Part 3, Step 1)
- The Docker Hub repository is **Public** - private images need registry credentials on Render
- The image was built with `--platform linux/amd64` - Render only runs `linux/amd64` images

**Render's free plan (check the current details on [render.com/docs/free](https://render.com/docs/free)):**

| | Free limit |
|---|---|
| Web service | 750 instance hours per month. **Spins down after 15 minutes without traffic**; the next request wakes it up, which takes about a minute |
| PostgreSQL | **Expires 30 days after you create it**, then a 14-day grace period before it is deleted with its data. Only **one** free database per workspace. 1 GB storage, no backups |

The spin-down is normal on the free plan - if your app is slow to answer the first time, wait a minute and try again.

---

## Part A - Deploy devops-demo from Docker Hub

### Step A1: Create a Render Account

1. Go to [https://render.com](https://render.com)
2. Click **"Get Started"** (or **"Sign In"**)
3. Sign up with **GitHub** (recommended) or your email
4. Verify your email if prompted

You land on the Render **Dashboard**, where your services and databases are listed.

> Keep this account - you can use it again in later lessons and for your module assignment.

### Step A2: Create a Web Service from Your Docker Image

1. In the Dashboard, click **"+ New"** → **"Web Service"**
2. Under **Source Code**, click **"Existing Image"**
3. In **Image URL**, enter your image:
   ```
   docker.io/YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0
   ```
   (`docker.io/` is optional - Render uses Docker Hub by default. Leave the credential empty for a public image.)
4. Click **"Connect"**. Render checks that it can pull the image.
5. Fill in the service details:
   - **Name:** `devops-demo`
   - **Region:** **Singapore** (or the region closest to you). **Remember it** - your database must use the same region in Part B
   - **Instance Type:** **Free**
6. Under **Environment Variables**, add one variable:

   | Key | Value |
   |---|---|
   | `PORT` | `8080` |

   **Why?** Render sends web traffic to the port in the `PORT` variable, which is `10000` by default. Spring Boot listens on `8080`. Render can usually detect `8080` on its own, but setting `PORT` makes sure the traffic goes to the right port.

7. Click **"Deploy Web Service"** (or **"Deploy"**)

Render pulls your image from Docker Hub and starts the container.

### Step A3: Watch the Deployment Logs

Render opens the service page. Click **"Logs"** in the left pane to follow the deploy:

```
==> Pulling image docker.io/YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0
==> Deploying...

 :: Spring Boot ::

Tomcat started on port 8080 (http) with context path '/'
Started DevopsDemoApplication in 2.9 seconds
==> Your service is live 🎉
```

It usually takes 1-3 minutes. The **Events** page (called **Deploys** in some versions of the dashboard) lists each deploy and whether it succeeded.

### Step A4: Test Your Application

1. Copy your service URL from the top of the service page. It looks like:
   ```
   https://devops-demo-xxxx.onrender.com
   ```
2. Add `/hello` to the end of the URL and open it in your browser, or use curl:
   ```bash
   curl https://YOUR-SERVICE.onrender.com/hello
   ```
3. You should see:
   ```
   DevOps demo - now on Docker Hub!
   ```

**Success!** devops-demo is running on Render. ✅ Go back to [lesson.md](./lesson.md) Part 3.

### Step A5: Explore Render's Features

- **Events:** history of every deploy
- **Logs:** the container's output (everything Spring Boot prints)
- **Environment:** environment variables for database URLs, API keys and other settings
- **Metrics:** CPU, memory and network usage
- **Settings:** the image URL, region, instance type, and the option to delete the service
- **Manual Deploy** (top right): **"Deploy latest reference"** pulls the image again and redeploys it

> Render does **not** watch Docker Hub. If you push a new image, you have to redeploy (Manual Deploy) or change the image tag yourself - you'll do this in Part B.

### Step A6: Troubleshooting

**`exec format error` in the logs**
- **Cause:** the image was built for ARM (Apple Silicon Mac) instead of `linux/amd64`
- **Solution:** rebuild with `docker build --platform linux/amd64 ...`, push again, then click **Manual Deploy → Deploy latest reference**

**"Image Pull Failed" in Events**
- **Cause:** the image name or tag is wrong, or the repository is private
- **Solution:** check the image and the `v4.10.0` tag exist on [Docker Hub](https://hub.docker.com), the name is `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.0`, and the repository is **Public**. Fix the image URL in **Settings**, then redeploy

**Deploy fails with "no open ports detected" / port scan timeout**
- **Cause:** Render is sending traffic to the wrong port
- **Solution:** check the **Environment** page has `PORT` = `8080`, then redeploy

**The first request takes about a minute, or shows a loading page**
- **Cause:** the free service spun down after 15 minutes without traffic
- **Solution:** nothing to fix - wait for it to wake up, then try again

---

## Part B - Add PostgreSQL and Update to v4.10.1

Do lesson.md Part 4, **Step 1** first: build and push `YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1`.

> Render does not notice the new tag on its own, so pushing `v4.10.1` doesn't change the running app. You'll switch to it in Step B4, after the database variables are set.

```
Render (one region, e.g. Singapore)
┌──────────────────────────────────────────────────────────┐
│                                                          │
│   devops-demo  ───── private network ─────►  PostgreSQL  │
│   (web service, devops-demo:v4.10.1)         (database)  │
│                                                          │
└──────────────────────────────────────────────────────────┘
        ▲
   Public URL (/hello)
```

### Step B1: Create a PostgreSQL Database

1. In the Dashboard, click **"+ New"** → **"Postgres"**
2. Fill in:
   - **Name:** `devops-demo-db`
   - **Database** and **User:** leave empty - Render generates them
   - **Region:** the **same region as your devops-demo web service** (for example, Singapore)
   - **PostgreSQL Version:** leave the default
   - **Plan / Instance Type:** **Free**
3. Click **"Create Database"**

Wait until the database status shows **Available** (usually a minute or two).

> **Remember:** a free Render database expires after 30 days. That's fine for this lesson - delete it when you're done (see Clean Up).

### Step B2: Find the Connection Details

1. Open the **devops-demo-db** database
2. Find the connection details on its **Info** page (or the **Connect** menu at the top right → **Internal** tab)

You'll see values like these:

| Field | Example value |
|---|---|
| Hostname | `dpg-abc123xyz-a` |
| Port | `5432` |
| Database | `devops_demo_db_x1y2` |
| Username | `devops_demo_db_user` |
| Password | `<generated-password>` |
| Internal Database URL | `postgresql://devops_demo_db_user:<password>@dpg-abc123xyz-a/devops_demo_db_x1y2` |

Use the **internal** values. The internal hostname only works for services in the same region, and keeps the traffic on Render's private network.

> **Important:** the Internal Database URL is **not** in the format Spring Boot needs. Spring Boot needs a **JDBC URL** that starts with `jdbc:postgresql://` and has no username or password in it. So we build our own URL from the individual values in the next step.

### Step B3: Connect devops-demo to the Database

1. Open the **devops-demo** web service
2. Click **"Environment"** in the left pane
3. Click **"+ Add Environment Variable"** and add these three, using **your** values from Step B2:

| Key | Value |
|---|---|
| `SPRING_DATASOURCE_URL` | `jdbc:postgresql://<Hostname>:5432/<Database>` - e.g. `jdbc:postgresql://dpg-abc123xyz-a:5432/devops_demo_db_x1y2` |
| `SPRING_DATASOURCE_USERNAME` | your **Username** |
| `SPRING_DATASOURCE_PASSWORD` | your **Password** |

Keep the `PORT` = `8080` variable from Part A.

4. Click the save button's arrow and choose **"Save only"** - you'll deploy in the next step, together with the new image

> **Copied, not referenced.** In the dashboard, Render doesn't link one service's variables to another's, so you paste the values yourself. If the database password ever changes, update `SPRING_DATASOURCE_PASSWORD` too. (Render can link them automatically in a `render.yaml` Blueprint - not covered in this lesson.)

### Step B4: Update to v4.10.1 and Deploy

1. Open the **devops-demo** web service → **"Settings"**
2. In the **Deploy** section, find **Image URL** and click **"Edit"**
3. Change the tag at the end from `v4.10.0` to `v4.10.1`:
   ```
   docker.io/YOUR_DOCKERHUB_USERNAME/devops-demo:v4.10.1
   ```
   (Or `docker.io/jsstrn/devops-demo:v4.10.1` if you used the instructor's image)
4. Click **"Save Changes"**
5. If a new deploy doesn't start by itself, click **Manual Deploy → "Deploy latest reference"**
6. Open **Logs** and follow the deploy

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
curl https://YOUR-SERVICE.onrender.com/hello
```

You should see the same message as in Part A. **Success!** devops-demo and its PostgreSQL database are both running on Render. 🎉 Go back to [lesson.md](./lesson.md) Part 4.

### Step B6: Troubleshooting

**`'url' must start with "jdbc"`**
- **Cause:** `SPRING_DATASOURCE_URL` is set to the Internal Database URL (`postgresql://...`)
- **Solution:** use `jdbc:postgresql://<Hostname>:5432/<Database>`

**`UnknownHostException: dpg-...` or "Connection refused"**
- **Cause:** the app can't reach the database's internal hostname
- **Solution:** check the web service and the database are in the **same region** (you can't change a region later - if they differ, create a new database in the web service's region); check the database status is **Available**; check the hostname has no typos

**`password authentication failed for user ...`**
- **Cause:** the username or password variable is wrong
- **Solution:** copy the **Username** and **Password** from the database page again - no extra spaces or quotes - then save and redeploy

**`Could not resolve placeholder 'SPRING_DATASOURCE_URL'`**
- **Cause:** the `v4.10.1` image is running, but the variables are missing or were added to the wrong service
- **Solution:** add the variables to the **devops-demo** web service (not the database), then **Save and deploy**

---

## Clean Up (Optional)

- **Database:** open **devops-demo-db** → **Settings** → **Delete Database**. Your free database expires after 30 days anyway, and you can only have one free database at a time
- **Web service:** open **devops-demo** → **Settings** → **Delete Web Service**. A free web service doesn't cost anything while it's spun down, so you can also keep it
