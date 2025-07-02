## 🚀 A User-Friendly Guide to the WSU Kamiak Supercomputer

Hey Nerds! This guide walks you from “What the heck is Kamiak?” to “Behold! My job completed with zero errors.”
Don't worry I'm a nerd too \:p
------------------------------

### 1. What Is Kamiak … and Why Should You Care?

* **Kamiak = a bazillion tiny computers** (nodes) joined by **invisible cluster magic**.
* No clicking icons here—everything’s done via command-line voodoo and a scheduler.
* **When to use it**:

  * 📊 Analyzing gargantuan datasets
  * 🔬 Running physics/chemistry simulations
  * 🤖 Training your next ML masterpiece
  * ⚙️ Parallelizable tasks (i.e., embarrassingly parallel)

> **Pro Tip:**
> The **login node** (kamiak.wsu.edu) is your front porch—use it for light work (editing, compiling).
> The **compute nodes** are the heavy-lifters—reserve them for your big jobs.

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

> **Note:** If WSL fails to load (because Hyper-V and Virtual Machine features are only available on Windows Pro), you may need to upgrade to Windows **Pro** edition to enable WSL properly.

> **X11 Forwarding:** Want to run GUI apps from Kamiak?
>
> Install [VcXsrv](https://sourceforge.net/projects/vcxsrv/) or [XMing](https://sourceforge.net/projects/xming/), then start it **before** connecting.
>
> Use `ssh -X your.wsu.id@kamiak.wsu.edu` to forward GUI apps like MATLAB.

#### macOS / Linux (Because it’s basically Unix already)

1. Open **Terminal**.
2. Run `ssh your.wsu.id@kamiak.wsu.edu`.
3. Say **yes**, type your password, and voilà, you’re in.

> For GUI apps: Install [XQuartz](https://www.xquartz.org/), then run:
>
> ```bash
> defaults write org.macosforge.xquartz.X11 enable_iglx -bool true
> ssh -X your.wsu.id@kamiak.wsu.edu
> ```

---

### 3. The Kamiak Filesystem & Your “Home”

* **Home**: `/home/your.wsu.id`

  * Backed up, but limited.
* **Scratch**: `/scratch/your.wsu.id`

  * **NOT** backed up, auto-deleted after 21 days.
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

> **Bonus:** Need temporary, high-speed space?
>
> ```bash
> export myScratch=$(mkworkspace)
> lsworkspace  # List your workspaces
> ```
>
> They expire in \~14 days. Use with care!

---

### 4. Moving Files (scp ≠ “soaping”)

Run these **locally**—not after you SSH in.

```bash
# Upload to Kamiak
scp my_script.py your.id@kamiak.wsu.edu:~/  

# Download from Kamiak
scp your.id@kamiak.wsu.edu:~/results.txt .
```

> **Option B:** Drag-and-drop with **FileZilla** or **Cyberduck**. Pretty GUI, same creds.

> **Power Users:** Use `rsync` for syncing:
>
> ```bash
> rsync -avz ./localdir your.id@kamiak.wsu.edu:~/remotedir
> ```
>
> Only copies what changed. Super efficient.

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

> **Note:** If `module avail` fails inside a batch script, it's not you. SLURM just gets cranky about it.

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

*See full examples in section 9.3 below!*

```bash
sbatch run_kamiak.sh
squeue -u your.wsu.id     # see queued/running jobs
sacct -u your.wsu.id      # see history
scancel JOBID             # kill it, if needed
scontrol show job JOBID   # detailed job info
```

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

---

#### 9.1 Your `load.txt` Quick-Start

When you first SSH in, you might run:

```bash
# “cat load.txt” – AKA my personal brain dump
module avail
module load StdEnv intel/25.0 gcc/14.2 python3/3.11.4 cuda/12.2.0 cudnn/8.9.7_cuda12.2

# Job monitoring 101
squeue -u nathan.balcarcel
squeue -a
scancel [JOB_ID]

# Elapsed time & status for a finished job
sacct -j [JOB_ID] --format=Elapsed
sacct --format=Elapsed,State -j [JOB_ID]
sacct --helpformat

# Launch my custom Python-venv alias
pyv grapes
```

> **Heads up:**
>
> * The first `module avail` shows everything installed.
> * The `module load …` line is exactly what I always use—feel free to swap in your favorite compiler, Python, CUDA, etc.
> * You can’t run `module avail` inside a batch script, FYI—I’ve tried, it throws tantrums.

---

#### 9.2 My `.bashrc` Snippets

*Stick this in your `~/.bashrc` for fun colors & a sweet `pyv` alias.*

```bash
# Source global definitions if they exist
if [ -f /etc/bashrc ]; then
    . /etc/bashrc
fi

# Fancy prompt coloring
RESET_="\[$(tput sgr0)\]"
BOLD_="\[$(tput bold)\]"
HOSTNAME_="\[$(tput setaf 10)\]"
DIR_="\[$(tput setaf 8)\]"
WHITE_="\[$(tput setaf 15)\]"

PS1="${BOLD_}${WHITE_}\u${HOSTNAME_}@\h${WHITE_}:${DIR_}\w${WHITE_}$ ${RESET_}${WHITE_}"

# Aliases
alias ls="ls --color=auto"
alias la="ls -a --color=auto"
alias py="python3"
alias da="deactivate"

# Quick-launch Python venv
pyv() {
    VENVPATH="${HOME}/venvs/$1"
    if [ -d "$VENVPATH" ]; then
        source "$VENVPATH/bin/activate"
    else
        echo "⚠️  Could not find virtual environment: $VENVPATH"
    fi
}

# Cargo stuff (if you use Rust)
. "$HOME/.cargo/env"

# Add custom apps to PATH
export PATH="$PATH:~/Applications"
```

> **Pro Tip:**
> Create a `~/venvs/` folder, then do `python3 -m venv ~/venvs/grapes`. After that, `pyv grapes` is magic.

---

#### 9.3 A Sample `kamiak.srun`

*Save this as `run_kamiak.sh` (or whatever floats your boat) and `chmod +x` it:*

```bash
#!/bin/bash
#SBATCH --partition=kamiak         # Queue
#SBATCH --job-name=myJob           # Job name
#SBATCH --output=result.out        # Stdout
#SBATCH --error=errors.err         # Stderr
#SBATCH --mail-type=ALL            # Notifications: BEGIN,END,FAIL,ALL
#SBATCH --mail-user=your.name@wsu.edu
#SBATCH --time=1-00:00:00          # 1 day
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=10
#SBATCH --gres=gpu:tesla:1         # Request 1 GPU (Tesla)

# Your commands go here. Example:
module load python3/3.11.4
pyv grapes
srun python my_heavy_script.py
```

After you `sbatch run_kamiak.sh`, you’ll get a job ID—then:

```bash
echo "Check status: sacct --format=Elapsed,State -j <YOUR_JOB_ID>"
```

And voilà, you’re tracking progress like a pro.

---

That’s all, folks! 🚀 Now get in there, mess around, and may your compute nodes be ever in your favor.


Practice makes perfect—log in, transfer files, load modules, and submit a job or two. Soon you’ll be running simulations so big, your laptop will feel like a potato next to Kamiak. Good luck, and may your job queues be ever short! 🎉

