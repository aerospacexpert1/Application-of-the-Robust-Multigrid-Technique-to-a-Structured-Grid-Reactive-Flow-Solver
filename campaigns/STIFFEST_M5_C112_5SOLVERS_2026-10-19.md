# S3 high-stiffness, refined mesh, 112 OpenMP threads

[Download ZIP](./STIFFEST_M5_C112_5SOLVERS_2026-10-19.zip) · [SHA256](./STIFFEST_M5_C112_5SOLVERS_2026-10-19.zip.sha256)

Five production runs: RMT FINALVOL2, MG2-V, MG2-W, MG3-V, and SG-RBGS. Each runs once on S3_HIGH_A1e9_Ta100, 1728×576 cells, 112 threads. This is the main reacting-flow solver campaign, not MMS Benchmark C. OpenFOAM is outside this campaign.

The mesh contains 995,328 cells, four times the 864×288 pilot. Physical domain, final time2s, chemistry, transport, time-step controls, solver algorithms and numerical tolerances remain unchanged. All numerical sources and per-solver launchers are byte-identical to the previous M4 package. The production wall limit is24h per task; the array is0-4%1 (one112-CPU Orfoz node at a time). There are no extra cases, thread levels or automatic repetitions.

## Download and submit on TRUBA

```bash
cd /arf/scratch/ozayifoglu
base_url="https://raw.githubusercontent.com/aerospacexpert1/Application-of-the-Robust-Multigrid-Technique-to-a-Structured-Grid-Reactive-Flow-Solver/main/campaigns"
package="STIFFEST_M5_C112_5SOLVERS_2026-10-19"
curl -fL --retry 3 "$base_url/$package.zip" -o "$package.zip"
curl -fL --retry 3 "$base_url/$package.zip.sha256" -o "$package.zip.sha256"
sha256sum -c "$package.zip.sha256" &&
unzip "$package.zip" &&
cd "$package" &&
bash submit_all.sh
```

Submit once. The command sends an Orfoz build job and a five-task array with an afterok dependency. Production runs begin only after compilation, the original RMT self-tests/fairness/equivalence checks, and the actual112-thread probe succeed. No application smoke cases are automatically added. The24h wall limit is a scheduling choice, not a guarantee of convergence or completion.

```bash
squeue -u "$USER"
python3 collect_summary.py
```

The combined C112_campaign_summary.csv contains five planned rows and distinguishes COMPLETE, NONCONVERGED, INVALID, FAILED, RUNNING_OR_INCOMPLETE and MISSING. Every recorded pressure solve is checked; a good final residual does not hide an earlier failure. Existing settings are retained, including MG cycle cap500 and SG sweep cap500000. The earlier M4 MG2-V one-step residual failure has not been hidden by retuning.

Local QA covers source hashes, refinement/hierarchy limits, source generation, shell syntax, and all five real Bash launchers with stub executables. Actual Linux/GCC/112-core CFD runs are performed on TRUBA; local QA is not a timing measurement. Detailed provenance and QA files are inside the ZIP.
