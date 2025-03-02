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
