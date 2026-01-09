# Concept: Strategy Matrix in GitHub Actions  

---

## Overview  
This demo shows how to **scale a single job across many OS / runtime combinations** without wasting runner minutes.  
You’ll fork this repo, inspect the pre-built `strategy.matrix` workflow, then make a few edits to watch how **`fail-fast`**, **`continue-on-error`**, **`max-parallel`**, **`include`**, and **`exclude`** change the run behavior.  
No local setup is required, the code only prints “OK.” You just need a GitHub account with Actions enabled.

---

## Step-by-Step

1. **Create your own copy**  
   - Click **Use this template** → **Create a new repository**.  
   - Name it anything you like and leave it **Public** (Actions are free for public repos).  

2. **Open the workflow file**  
   - Navigate to `.github/workflows/matrix.yml`.  
   *This is the only file you’ll edit.*

3. **Observe the default matrix**  
   - Two OSes (`ubuntu-latest`, `windows-latest`).  
   - Two Node versions (`18`, `20`).  
   - One extra “experimental” combo added under `include`.  
   *Goal: understand what will run before changing anything.*

4. **Run the workflow once**  
   - Go to **Actions** → **Matrix Mastery** → **Run workflow** → **Run workflow**.  
   - You should see **5 jobs** (4 regular + 1 experimental).  

5. **Watch the grid while it runs**  
   - Only **2 jobs** run at the same time (`max-parallel: 2`).  
   - The experimental job fails, but the workflow still passes (`continue-on-error`).  

6. **Edit ① – Exclude a combo**  
   - In `matrix.yml`, find the `exclude:` section and add:  
     ```yaml
     exclude:
       - os: ubuntu-latest
         node: 20
     ```  
   - Commit the file.  
   *You just removed the ubuntu + Node 20 permutation.*

7. **Edit ② – Add a new axis value**  
   - Still in `matrix.yml`, change  
     ```yaml
     experimental: [false]
     ```  
     to  
     ```yaml
     experimental: [false, true]
     ```  
   - Commit.  
   *This doubles the matrix by adding a second value to the `experimental` axis.*

8. **Edit ③ – Turn on fail-fast and force a real failure**  
   1. Set `fail-fast: true` in the `strategy` section.  
   2. At the **top** of your `steps:` list, insert a temporary failing step:  
      ```yaml
      - name: Temporary failure
        run: exit 1   # intentional break
      ```  
   3. Commit and run the workflow again.  
   *Watch: one job fails immediately and the rest cancel, saving time.*

9. **Undo the temporary breakage**  
   - Delete the `Temporary failure` step (or change `exit 1` back to `echo OK`).  
   - Commit so the workflow is green again.

