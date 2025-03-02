# Learning YAML
This is the second grouping of projects where I try to learn YAML. The ultimate goal is to be able to use YAML to deploy a github secret. I have certain programs that only work with API keys, and I don't want those public, so I'm learning YAML to combat this. Eventually, I hope to use YAML to pull a secret from my repo secrets and use it when the program is called, while keeping the API hidden throughout.

## **Project 1️⃣ - Auto-Generate a File**
🔹 **Goal:** Make a GitHub Action that runs every time you push code, creates a file (`timestamp.txt`), writes the current date/time into it, and commits it back to your repo.  

### **Step 1: Create Your GitHub Workflow Folder**
1. Go to your repo on GitHub.  
2. Click on the **Code** tab.  
3. Click the **Add file** button → **Create new file**.  
4. Name the file:  
   ```
   .github/workflows/generate-file.yml
   ```
   - The `.github/workflows/` folder is where GitHub looks for workflow files.  
   - The `generate-file.yml` is the name of this workflow. You can name it whatever, but keep the `.yml` extension.  

---

### **Step 2: Add This YAML Code to Your Workflow File**
Copy this and paste it inside `generate-file.yml`:  

```yaml
name: Generate Timestamp File

on: 
  push:
    branches:
      - main  # Runs when you push to the main branch

jobs:
  create-file:
    runs-on: ubuntu-latest  # The virtual machine the job runs on

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3  # Pulls your repo into the VM

      - name: Generate timestamp file
        run: echo "Generated on $(date)" > timestamp.txt  # Creates the file

      - name: Commit and push the file
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add timestamp.txt
          git commit -m "Auto-generated timestamp"
          git push
```

---

### **Step 3: Break Down WTF Is Happening**
This workflow does **3 major things**:  
1️⃣ **Runs on every push to `main`**  
   - The `on: push` part means this workflow triggers when you push changes.  
   - The `branches: - main` means it only runs when pushing to `main`.  

2️⃣ **Creates a new file (`timestamp.txt`)**  
   - The `run: echo "Generated on $(date)" > timestamp.txt` writes the date into a new file.  
   - `$(date)` fetches the current timestamp.  

3️⃣ **Commits and pushes the new file to the repo**  
   - It configures Git with a fake bot account (`github-actions[bot]`).  
   - Runs `git add timestamp.txt` to stage the new file.  
   - Runs `git commit -m "Auto-generated timestamp"` to commit it.  
   - Runs `git push` to push the changes to GitHub.  

---

### **Step 4: Commit & Test It**
1. Click the **Commit new file** button at the bottom.  
2. Push a change to your repo (modify any file and commit it).  
3. Go to **Actions** tab on GitHub → Click the `Generate Timestamp File` workflow.  
4. Once it runs successfully, check your repo for the new `timestamp.txt` file.


---

## **Project 2️⃣ - Using GitHub Secrets**  
🔹 **Goal:** Store a **secret API key** in GitHub Secrets and use it inside a workflow to create a config file (`config.json`).  

### **Why This Matters**  
- **Hides sensitive data** 🔒 (so you don’t accidentally commit your API key).  
- **Prepares you for your final project**, where you'll inject an API key into a file for your movie recommendation system.  

---

## **Step 1: Add a GitHub Secret**
1. **Go to your repo on GitHub.**  
2. Click on **Settings** (top bar).  
3. On the left sidebar, go to **Secrets and variables** → **Actions**.  
4. Click **New repository secret**.  
5. **Name it**: `MY_SECRET_API_KEY`  
6. **Value**: Enter any fake API key for now, like:  
   ```
   12345-FAKE-API-67890
   ```
7. Click **Add secret**.  

---

## **Step 2: Create Your Workflow File**
Like before, go to `.github/workflows/` and create a new file:  
```
.github/workflows/use-secret.yml
```

Copy-paste this YAML into it:  

```yaml
name: Use GitHub Secret

on: 
  push:
    branches:
      - main  # Runs when you push to main

jobs:
  create-config:
    runs-on: ubuntu-latest  # The VM GitHub uses

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3  # Pulls repo so we can modify files

      - name: Create config file with secret
        env:
          API_KEY: ${{ secrets.MY_SECRET_API_KEY }}
        run: |
          echo '{ "apiKey": "'"$API_KEY"'" }' > config.json

      - name: Commit and push config file
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add config.json
          git commit -m "Generated config file with secret" || echo "No changes to commit"
          git push https://x-access-token:${GITHUB_TOKEN}@github.com/${{ github.repository }}.git
```

---

## **Step 3: What’s Happening Here?**
🔥 **Same structure as before, but now we're injecting a secret**!  

1️⃣ **Triggers on push to `main`**  
2️⃣ **Pulls the repo** (`actions/checkout@v3`) so we can modify files.  
3️⃣ **Creates `config.json` dynamically**, using your secret from GitHub:  
   - `env: API_KEY: ${{ secrets.MY_SECRET_API_KEY }}` pulls in your secret.  
   - The `run:` block uses `echo` to write the secret inside a JSON file.  
   - The `config.json` will look like this after execution:  
     ```json
     {
       "apiKey": "12345-FAKE-API-67890"
     }
     ```
4️⃣ **Commits & pushes `config.json`** using the same method we used in Project 1.  

---

## **Step 4: Commit & Test It**
1. **Commit the workflow file** (`use-secret.yml`).  
2. Push any change to `main` (or just trigger the action manually from the Actions tab).  
3. Go to the **Actions tab** in GitHub → Click `Use GitHub Secret` workflow.  
4. Once it runs, check your repo for `config.json`.  

---

## **🔥 Challenge Mode (Try This)**
- **Try adding multiple secrets** (like `API_URL`, `DB_PASSWORD`) and inject them into `config.json`.  
- **Modify the workflow** to overwrite `config.json` every time it runs.  
- **Modify it to delete `config.json` after running** (so it doesn’t stay in your repo).  
