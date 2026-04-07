# 🚀 GitHub to VPS Deployment Guide using Aapanel + GitHub Actions

---

## ✅ Step 1: First Login to Aapanel
Log in to your Aapanel to manage your web server environment.

---

## 📂 Step 2: Create a Project Repository
- Create a private or public repository on GitHub for your project (e.g., `cd`).
- Make sure you have added your project files and pushed them to the `main` branch.

---

## 🧰 Step 3: One-Time Setup (If Git Is Not Installed on VPS)

Run the following command on your VPS:

```bash
apt install git -y
```

---

## 🔑 Step 4: Generate SSH Key on VPS

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/CICD
```

Press Enter when prompted to confirm the path. You’ll get two files:
- Private key: `~/.ssh/CICD`
- Public key: `~/.ssh/CICD.pub`

---

## 📋 Step 5: Copy & Register the SSH Public Key

1. Display the public key:
    ```bash
    cat ~/.ssh/CICD.pub
    ```
2. Append it to authorized keys:
    ```bash
    cat ~/.ssh/CICD.pub >> ~/.ssh/authorized_keys
    ```
3. Go to **GitHub → Settings → SSH and GPG keys**
4. Click **"New SSH Key"**
5. **Title:** `VPS`
6. **Paste** the copied key
7. **Save**

8. Test the connection:
    ```bash
    ssh -T git@github.com
    ```

---

## 🌐 Step 6: Clone Repository to Aapanel Project Directory

```bash
cd /www/wwwroot/test.mirpurianscafe.com
git clone git@github.com:MahfujuRahman/cd.git .
```

---

## 🛠️ Step 7: Add GitHub Actions Workflow File

In your project folder, create the file:

`.github/workflows/deploy.yml`

```yaml
name: Project Deployment

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy using ssh
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          port: 22
          script: |
            # Add repository as safe (fixes "dubious ownership" error)
            git config --global --add safe.directory ${{ secrets.PROJECT_PATH }}
            cd ${{ secrets.PROJECT_PATH }}
            git pull origin main
```

---

## 🔐 Step 8: Add GitHub Secrets

Go to your GitHub repository → **Settings → Secrets and variables → Actions**  
Click **"New repository secret"** for each of the following:

| Name              | Value                                                       |
|-------------------|-------------------------------------------------------------|
| `SSH_PRIVATE_KEY` | Content of `~/.ssh/CICD` (the **private key**, not `.pub`) |
| `VPS_HOST`        | Your VPS IP (e.g., `20.2.4.20`)                             |
| `VPS_USER`        | Your SSH username (e.g., `root`)                            |
| `PROJECT_PATH`    | Path to your project on VPS (e.g., `/www/wwwroot/test.mirpurianscafe.com`) |

---

After this setup, every push to the `main` branch will automatically deploy your code to your VPS via SSH. ✅
