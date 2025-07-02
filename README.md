## 🚀 A User‑Friendly Guide to the WSU Kamiak Supercomputer

Hey Nerds! This guide walks you from “What the heck is Kamiak?” to “Behold! My job completed with zero errors.”
Don't worry I'm a nerd too :p
---

### 1. What Is Kamiak … and Why Should You Care?

* **Kamiak = a bazillion tiny computers** (nodes) joined by **invisible cluster magic**.
* No clicking icons here—everything’s done via command‑line voodoo and a scheduler.
* **When to use it**:

  * 📊 Analyzing gargantuan datasets
  * 🔬 Running physics/chemistry simulations
  * 🤖 Training your next ML masterpiece
  * ⚙️ Parallelizable tasks (i.e., embarrassingly parallel)

> **Pro Tip:**
> The **login node** (kamiak.wsu.edu) is your front porch—use it for light work (editing, compiling).
> The **compute nodes** are the heavy‑lifters—reserve them for your big jobs.

---

### 2. Connecting to Kamiak

You need two ingredients: **SSH** + your **Terminal**.

```bash
ssh your.wsu.id@kamiak.wsu.edu
```

#### Windows PowerShell (Noobs’ Edition)

1. Open **PowerShell**.
2. Paste: `ssh cougar.creative@kamiak.wsu.edu`
3. Type **yes** on host authenticity prompt.
4. Blindly enter your WSU password (nobody sees it—even you).

#### Windows Subsystem for Linux (Pro Edition)

1. Run (as Admin):

   ```powershell
   wsl --install
   ```
2. Restart, launch **Ubuntu**, set up a Linux user.
3. In Ubuntu shell, same SSH command as above.

> **Note:** If WSL fails to load (because Hyper‑V and Virtual Machine features are only available on Windows Pro), you may need to upgrade to Windows **Pro** edition to enable WSL properly.

#### macOS / Linux (Because it’s basically Unix already)

1. Open **Terminal**.
2. Run `ssh your.wsu.id@kamiak.wsu.edu`.
3. Say **yes**, type your password, and voilà, you’re in.

---

### 3. The Kamiak Filesystem & Your “Home”

* **Home**: `/home/your.wsu.id`

  * Backed up, but limited.
* **Scratch**: `/scratch/your.wsu.id`

  * **NOT** backed up, auto‑deleted after 21 days.
* **Data**: `/data/…`

  * Shared, backed up; ideal for big group projects.

#### Handy Commands

```bash
pwd         # Where am I?
ls          # What’s here?
cd …        # Go somewhere else
mkdir foo   # Make a new folder
cp a b      # Copy a→b
mv a b      # Move/rename
rm file     # Delete (use with caution…)
```

---

### 4. Moving Files (scp ≠ “soaping”)

Run these **locally**—not after you SSH in.

```bash
# Upload to Kamiak
scp my_script.py your.id@kamiak.wsu.edu:~/  

# Download from Kamiak
scp your.id@kamiak.wsu.edu:~/results.txt .
```

> **Option B:** Drag‑and‑drop with **FileZilla** or **Cyberduck**. Pretty GUI, same creds.

---

### 5. Software Modules: “module load = instant superpowers”

* **List everything**:

  ```bash
  module avail
  ```
* **Search for a tool**:

  ```bash
  module spider python
  ```
* **Load it** (e.g., Python 3.9.1):

  ```bash
  module load python/3.9.1
  ```
* **See what you’ve got**:

  ```bash
  module list
  ```
* **Unload**:

  ```bash
  module unload python
  module purge   # fresh slate
  ```

---

### 6. Running Jobs: SLURM to the Rescue

#### 🔧 Interactive (IDEV)

For quick tests & debugging:

```bash
idev --time=01:00:00 --cpus-per-task=8
# …then you’re on compute node cnXXX—play away!
exit  # when done
```

#### 📜 Batch (SBATCH)

For “set it and forget it” runs.

1. **hello\_kamiak.py**

   ```python
   import platform, os
   print("👋 Hello from Kamiak!")
   print("Node:", platform.node())
   print("SLURM Job ID:", os.getenv("SLURM_JOB_ID", "none"))
   ```

2. **run\_hello.sh**

   ```bash
   #!/bin/bash
   #SBATCH --job-name=hello_test
   #SBATCH --partition=kamiak
   #SBATCH --time=00:05:00
   #SBATCH --nodes=1
   #SBATCH --ntasks-per-node=1
   #SBATCH --cpus-per-task=1
   #SBATCH --mem=1G
   #SBATCH --output=%x_%j.out
   #SBATCH --error=%x_%j.err
   #SBATCH --mail-type=END,FAIL
   #SBATCH --mail-user=your.wsu.id@wsu.edu

   echo "Starting at $(date)"
   module load python/3.9.1
   srun python hello_kamiak.py
   echo "Done at $(date)"
   ```

3. **Submit & Monitor**

   ```bash
   sbatch run_hello.sh
   squeue -u your.wsu.id     # see queued/running jobs
   sacct -u your.wsu.id      # see history
   scancel JOBID             # kill it, if needed
   ```

> **Note:** After completion, check `<jobname>_<jobid>.out` and `.err` for output and errors.

---

### 7. Be a Good Cluster Citizen

* **Ask for only what you need**
  🚫 Don’t request 7 days & 128 GB RAM if you need 2 hrs & 4 GB.
* **Use `/scratch` for heavy I/O**
  Faster and won’t bloat your home space.
* **Backup your code** elsewhere—just in case.
* **Cite Kamiak** in any publications.
* **Ask HPC support** when you’re stuck—they’re actual humans.

---

### 8. Wrapping Up

Practice makes perfect—log in, transfer files, load modules, and submit a job or two. Soon you’ll be running simulations so big, your laptop will feel like a potato next to Kamiak. Good luck, and may your job queues be ever short! 🎉

