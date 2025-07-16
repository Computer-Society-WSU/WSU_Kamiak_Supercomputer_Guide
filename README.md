---

## 🚀 A User-Friendly Guide to the WSU Kamiak Supercomputer

*For scientists, analysts, and researchers who need serious compute power—no CS degree required.*
Whether you run climate models, molecular dynamics, or microscopy image processing, this guide will help you navigate Kamiak smoothly.

---

### 1. What Is Kamiak—and Why Use It for Research?

* **Kamiak** is a high-performance cluster: a fleet of networked computers (nodes) working together via SLURM scheduler magic.
* No GUIs—everything is done via the command line and batch scripts.
* **Ideal for research tasks**:

  * 📊 Large-scale data analysis (e.g., genomics, survey data)
  * 🔬 Scientific simulations (e.g., fluid dynamics, molecular modeling)
  * 🛰️ Geospatial processing
  * ⚙️ Parallel computations (e.g., parameter sweeps)

> **Pro Tip:**
>
> * **Login node** (`kamiak.wsu.edu`): light editing, code compilation, monitoring only.
> * **Compute nodes**: heavy lifting—run your simulations and analyses here.

---

### ⚠️ WARNING: Don’t Compile or Run Heavy Work on the Login Node

**What’s the login node?**
It’s the shared gateway where you SSH in. It’s meant for interactive tasks like editing scripts—not for memory‑ or CPU‑intensive research workloads.

**Why avoid it?**

* Resource contention can slow down or crash the gateway node for all users.
* Running big analyses there may lead to account suspension by HPC support.

**Do this instead:**

1. Log in and load necessary modules.
2. Launch an interactive session on a compute node:

   ```bash
   idev --time=02:00:00 --cpus-per-task=8
   # Now you’re on a compute node—run your compile or test here!
   exit
   ```
3. Or package your entire workflow into a batch script (see section 6) and submit with `sbatch`.

---

### 2. Connecting to Kamiak

#### macOS / Linux

```bash
ssh your.wsu.id@kamiak.wsu.edu
```

#### Windows PowerShell

```powershell
ssh your.wsu.id@kamiak.wsu.edu
```

(Accept host key, enter your password—quietly and securely.)

#### Windows WSL (You need Windows Pro/Education edition for this)

```powershell
wsl --install
# Reboot and launch Ubuntu
ssh your.wsu.id@kamiak.wsu.edu
```

> **GUI Forwarding for Visualization Tools**:
>
> * Windows: Install VcXsrv or XMing.
> * macOS: Install XQuartz.
>   Use `ssh -X …` to run tools like MATLAB or RStudio with GUI.

---

### 3. Navigating the File System as a Researcher

* **Home**: `/home/your.wsu.id` — backed up nightly, but limited space.
* **Scratch**: `/scratch/your.wsu.id` — high-speed I/O, **not** backed up, auto-deletes after 21 days—perfect for intermediate data.
* **Data**: `/data/...` — backed up, shared with collaborators on group projects.

```bash
pwd  # current directory
ls   # list files
cd   # change directory
mkdir dir  # make folder
df -h  # disk usage
grants insight on storage
```

Need extra temporary workspace?

```bash
export MY_SCRATCH=$(mkworkspace)
lsworkspace  # list your active workspaces
```

---

### 4. Transferring Files: Lab Data, Scripts, Results

**Always run these on your local machine, not on the login node**:

```bash
# Upload experimental scripts or data files
scp dataset.csv your.id@kamiak.wsu.edu:~/scratch/
# Download output files or logs
tail -n 50 ~/scratch/results.log  # preview before transferring
scp your.id@kamiak.wsu.edu:~/scratch/results.csv ./
```

**GUI option**: FileZilla or Cyberduck works with your WSU credentials.
**Efficient sync**: rsync only copies differences—great for large datasets:

```bash
rsync -avz ./local_data your.id@kamiak.wsu.edu:~/data/projectX
```

---

### 5. Software Modules for Research Environments

Kamiak provides scientific software via modules:

```bash
module avail             # list all available tools
module spider R          # search for R versions or packages
module load python/3.11  # load Python
module list              # see loaded modules
module unload python     # remove Python
module purge             # clear all modules
```

> **Tip:** Some modules (e.g., MPI, GPU libraries) must match your application’s version to avoid runtime errors.

---

### 5.1 Tools & Dependencies for Non‑Sudo environment

By default, all users do not have root access, which means we cannot use the 'sudo' command, because of this we have to create our own toolbox:

1. **Create a local install directory**:

   ```bash
   mkdir -p ~/tools/bin ~/tools/lib
   ```
2. **Update your environment** (`~/.bashrc`):

   ```bash
   export PATH="$HOME/tools/bin:$HOME/.local/bin:$PATH"
   export LD_LIBRARY_PATH="$HOME/tools/lib:$LD_LIBRARY_PATH"
   ```
3. **Install R/Python packages locally**:

   ```bash
   # R packages
   R -q -e 'install.packages("dplyr", lib="~/tools/lib/R")'
   # Python packages
   ```

echo 'export PATH="\$HOME/.local/bin:\$PATH"' >> \~/.bashrc
pip install --user pandas scipy

````
4. **Compile C/C++ research tools**:
```bash
cd ~/tools
git clone https://github.com/example/analysis-tool.git
cd analysis-tool && mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=$HOME/tools ..
make && make install
````

This way, you maintain all dependencies in your home space—no sudo needed.

---

### 6. Running Your Research Workloads with SLURM

#### Interactive Sessions (for testing pipelines)

```bash
idev --time=01:00:00 --cpus-per-task=4
# you’re now on a compute node—run your data prep or test scripts here!
exit
```

#### Batch Jobs (for full-scale experiments)

**Sample \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*`run_kamiak.sh`**:

```bash
#!/bin/bash
#SBATCH --partition=kamiak           # queue name
#SBATCH --job-name=researchTest      # job name
#SBATCH --output=results_%j.out      # stdout file (with job ID)
#SBATCH --error=errors_%j.err        # stderr file
#SBATCH --time=0-04:00:00            # runtime: hh:mm:ss
#SBATCH --nodes=1                    # number of nodes\ n#SBATCH --cpus-per-task=8            # CPUs per task
#SBATCH --mem=32G                    # memory request\ n#SBATCH --gres=gpu:1                 # GPU if needed

module load R/4.2.0
Rscript analysis_pipeline.R         # or your Python script
```

**Submit & Track**:

```bash
sbatch run_kamiak.sh                # submit job
squeue -u your.wsu.id               # view queued/running
sacct -u your.wsu.id                # view completed jobs and resources used
scancel <JOBID>                     # cancel if needed
```

> **Deep dive**: Add mail notifications (`--mail-type=END,FAIL`) and adjust resources as your workflows evolve.

---

### ✅ Hands-On: Submit Your First Research Job

1. Create a minimal `.sh` script:

   * Load your analysis software (e.g., R or Python).
   * Run a simple command (e.g., `Rscript -e 'print("Hello Kamiak Research")'`).
   * Request modest time (e.g., 30m) and memory.
2. Submit with `sbatch`, then run:

   ```bash
   squeue -u your.wsu.id  # in-progress
   sacct -u your.wsu.id   # after completion
   ```
3. Check the output file for your script’s printout.

---

### 7. Be a Good Cluster Research Citizen

* Request only what you need (time, memory, CPUs, GPUs).
* Use `/scratch` for large I/O—don’t clog your home directory.
* Regularly back up results elsewhere (Git, cloud).
* Cite Kamiak and WSU HPC in any publications.
* HPC support is friendly—reach out when stuck.

---

### 🏁 Summary for Researchers

* SSH into Kamiak, transfer data, and load modules.
* Set up your `~/tools` for custom dependencies.
* Write and submit SLURM batch scripts for your experiments.
* **Never compile or run resource-heavy tasks on the login node**—use `idev` or `sbatch`.

Now you’re ready to scale your science—may your analyses run smoothly and your results be groundbreaking! 🚀
