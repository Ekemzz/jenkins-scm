# jenkins-scm
A webhook is a way for one application to automatically send real-time data to another application when an event happens. Instead of constantly polling for updates, the source system (e.g., GitHub) sends an HTTP POST request to a specified URL (e.g., your Jenkins server) whenever the event occurs (like a code push).

In CI/CD, webhooks are commonly used so that when you push code to a repository, the repo immediately notifies Jenkins via the webhook URL, and Jenkins triggers a build or deployment. This makes workflows event-driven, faster, and more efficient.

Good question 👍 You’re basically asking how to let GitHub (or GitLab/Bitbucket) notify your **Jenkins server** whenever you push new code. That’s where a **webhook** comes in. Let me walk you through it step by step:

---

## 🔹 Step 1: Make sure Jenkins is reachable

* Jenkins by default runs on port **8080**.
* So if your Jenkins server has a public IP (say `3.85.123.45`), its URL will be:

  ```
  http://3.85.123.45:8080/
  ```
* Make sure your **security group** / firewall allows inbound traffic on port `8080`.

---

## 🔹 Step 2: Create/Configure a Jenkins job

1. In Jenkins, create a **Freestyle** or **Pipeline** job.
2. Under **Source Code Management (SCM)** → select **Git** → add your repo URL.
3. Under **Build Triggers**, check:

   * ✅ **GitHub hook trigger for GITScm polling** (if GitHub)
   * or ✅ **Build when a change is pushed to GitLab** (if GitLab plugin is installed).

---

## 🔹 Step 3: Set up the webhook in GitHub

1. Go to your repo → **Settings** → **Webhooks** → **Add webhook**.
2. In **Payload URL**, enter:

   ```
   http://<Jenkins-IP>:8080/github-webhook/
   ```

   Example:

   ```
   http://3.85.123.45:8080/github-webhook/
   ```
3. Set **Content type**: `application/json`.
4. Choose: **Just the push event**.
5. Save.

---

## 🔹 Step 4: Test it

* Push a change to your GitHub repo.
* GitHub will send a webhook POST request to Jenkins.
* Jenkins should trigger the build automatically.

You can also check webhook delivery logs in GitHub:
`Repo → Settings → Webhooks → Recent Deliveries`.

---

⚡ **Important notes:**

* If Jenkins is running behind a **reverse proxy** (like Nginx/Apache), you might expose it on port **80/443** instead of `8080`. In that case, your webhook URL could be `http://<domain>/github-webhook/`.
* If your Jenkins is on a **private server** (no public IP), you’ll need a tunnel (like **ngrok**) to expose it to GitHub.

---

