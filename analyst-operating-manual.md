# Analyst Operating Manual

**Role:** Junior Quantitative Research Analyst
**Setting:** Two-person research shop. You analyze; your boss sells the work to clients (RIAs, small funds, plant managers, operators). A senior analyst sits at the next desk and reviews everything you ship.
**Purpose:** Define the job, the tools, the standards, the daily ritual, and the format every project follows, so someone who knows nothing going in can sit in the chair and learn by doing the work.

---

## 1. What the job is

You take a vague question from someone with money on the line, turn it into a precise question, find the answer in data, and hand back something a non-analyst understands in thirty seconds. That sentence is the whole job. Everything below serves it.

You do not advise clients on what to trade, buy, or staff. You produce the analysis their decisions rest on. Cross that line and you are giving financial or management advice, which is a different licensed job with different liability. Stay on your side: you read the data and report what it says.

A client pays for three things, in this order:

1. **Correct.** The number is right and you can prove it.
2. **Clear.** They understand it without you in the room.
3. **Fast.** It arrived before they needed it.

Lose the first and nothing else counts.

---

## 2. The two domains: same loop, two dialects

You work across Lean operations and quantitative finance. The seven-step loop in section 6 never changes. What changes is the vocabulary, the typical question, and the traps. Learn both dialects and you can take either kind of brief.

### Lean operations and business

**The question usually sounds like:** where are we losing output, what is driving scrap or delay, where is the bottleneck, is this process stable or drifting.

**Vocabulary you answer in:**

- **Takt and cycle time:** the pace demand requires versus the pace you run.
- **Throughput and attainment:** units produced against target.
- **OEE:** Availability times Performance times Quality. The standard health score for a line.
- **Pareto (80/20):** most of the loss hides behind a few causes. Rank them and you find the fix.
- **Bottleneck / Theory of Constraints:** the slowest step sets the whole line's speed. Fix anything else and nothing improves.
- **Defect rate and DPMO:** how often quality fails.
- **DMAIC:** Define, Measure, Analyze, Improve, Control. The Six Sigma spine a green belt course teaches, and it maps cleanly onto the loop.
- **SPC and control charts:** is a swing normal variation or a real signal.

**Where the data lives:** MES and hour-by-hour tables, ERP exports (SAP MB51 and friends), time studies, quality logs, downtime trackers.

**Traps that produce wrong answers:** downtime reasons logged inconsistently between shifts, target definitions that drift over time, units mislabeled, shift and timezone boundaries that split a run, scrap counted twice.

### Quantitative finance

**The question usually sounds like:** how risky is this, how correlated are these, what is driving the returns, has the regime changed.

**Vocabulary you answer in:**

- **Returns:** simple versus log. Know which you are using and why.
- **Volatility:** realized, annualized. The standard measure of risk.
- **Drawdown:** how far it fell from a peak. What clients feel.
- **Sharpe ratio:** return earned per unit of risk taken.
- **Correlation and beta:** how two things move together, and how much one moves with the market.
- **Factors:** the Fama-French market, size, value, profitability, and investment factors. The canonical lens on what drives a portfolio.
- **Stationarity and fat tails:** financial data breaks the "normal distribution" assumptions people reach for. Markets have more extreme days than a bell curve predicts.

**Where the data lives:** FRED (macro and index levels), the Kenneth French Data Library (factors and portfolios), SEC EDGAR (company fundamentals), price feeds.

**Traps that produce wrong answers:** look-ahead bias (using information you would not have had at the time), survivorship bias (only studying the survivors), weekend and holiday gaps faked into real returns, mixing price levels with returns, sloppy annualization.

The operations example and the finance example in your library run the identical seven steps. That is the point. Once the loop is muscle memory, switching domains is switching dialects.

---

## 3. The kit

Five things. You install four locally in section 4. Add nothing else until a project forces it.

| Tool            | What it does                                     |
| --------------- | ------------------------------------------------ |
| Python + pandas | Analyzes data. Your main surface.                |
| JupyterLab      | The notebook. Where you do the work.             |
| matplotlib      | Draws the one chart.                             |
| git             | Saves and versions everything. Your undo button. |

**A database is deferred on purpose.** Your first projects read CSV files and URLs straight into pandas, which needs no server. When a project needs SQL, you add it then: DuckDB if you want serverless SQL against local files with zero setup, or Postgres when you meet it at a real shop. Not before.

**The minimalist rule.** A tool enters the kit when a project cannot be done without it, never on spec. When that day comes you will know exactly why, which is the only good reason to add anything. Later candidates and their trigger: polars (pandas got slow on a big set), plotly or Evidence (a client wants an interactive deliverable), streamlit (you ship a tool instead of a report), scipy or statsmodels (a project needs real statistics).

---

## 4. Setting up your laptop

You are mimicking a work-provided machine: one clean, self-contained workspace you can rebuild from scratch. This is on your Windows PC, in PowerShell or Windows Terminal. Do it once.

**Step 1. Confirm Python.**

```powershell
python --version
```

If that errors, install from python.org and check "Add Python to PATH" during install, then reopen the terminal.

**Step 2. Make your workspace and enter it.**

```powershell
mkdir C:\Users\you\analyst
cd C:\Users\you\analyst
```

**Step 3. Create an isolated environment.** This keeps your project's tools separate from the rest of the machine, exactly like a managed work laptop.

```powershell
python -m venv .venv
```

**Step 4. Activate it.**

```powershell
.venv\Scripts\Activate.ps1
```

If PowerShell blocks the script, run this once, then activate again:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

When it works, your prompt shows `(.venv)` at the front. That tells you the environment is on.

**Step 5. Install the kit.**

```powershell
pip install jupyterlab pandas matplotlib
```

**Step 6. Start version control.**

```powershell
git init
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

**Step 7. Launch the workspace.**

```powershell
jupyter lab
```

A browser tab opens. That is your desk. From now on, your morning is steps 2, 4, and 7.

**Step 8. Leaving the workspace.** When you are done for the day, close the browser tab, then in the terminal type `deactivate` to turn the virtual environment off. Your prompt loses the `(.venv)` prefix. The environment stays installed; you turn it back on tomorrow with step 4.

Drop the manual, the deliverable reference, and the two example notebooks into the `analyst` folder so they live next to your work.

### 4.1 Project folder layout

One folder per project, inside the workspace. The folder is the unit of work: it has the notebook, the data it pulled, the chart it produced, and the deliverable that left the building. When the project ships, you can hand someone the folder and they have everything.

```
analyst/
  .venv/                              the environment (never edit by hand)
  .git/                               version control (never edit by hand)
  analyst-operating-manual.md
  client-deliverable-reference.md
  current-project.md
  example-notebook-operations.ipynb
  example-notebook-finance.ipynb
  01-yield-curve/
    project-01-yield-curve.ipynb
    data/                             raw pulls, untouched
      DGS10.csv
      DTB3.csv
    chart.png                         the one chart, saved
    deliverable.md                    headline, chart link, three sentences, source
```

Two conventions to hold to. Raw data goes in `data/` inside the project folder and never gets edited in place; cleaning happens in the notebook so it is reproducible. Saved charts and the deliverable live at the project folder root, where they are easy to find when the boss asks for the file.

---

## 5. Tool primers, from zero

You have not used Jupyter, barely touched pandas and matplotlib, and never run git. Here is what you need to start. The example notebooks teach the rest by showing.

### 5.1 Jupyter in five minutes

- A notebook is a stack of **cells**. Two kinds: **code** cells run Python, **markdown** cells hold your text and notes.
- Run a cell with **Shift+Enter**.
- Behind the notebook runs the **kernel**: a live Python session that remembers everything you have run, in the order you ran it. That memory is the power and the danger.
- Cells run in the order *you run them*, not the order they sit on the page. Running cell 5 then cell 2 is the number-one beginner trap, because the result no longer matches what is written.
- The fix is one habit: before you trust or ship anything, **Restart Kernel and Run All Cells**. If it survives that, the notebook is honest.

### 5.2 git without fear

Your fear is reasonable and git is the cure for it. Think of git as a save-state system for a game. Every `commit` is a save point you can return to forever. **You cannot lose committed work.** The thing that bites people is not committing, never committing too much.

Five commands cover almost everything you will do:

```powershell
git status            # what changed since the last save
git add .             # stage everything for the next save
git commit -m "..."   # make a save point, with a message
git log --oneline     # list your save points, newest first
git restore .         # throw away UNSAVED changes, snap back to last commit
```

That last one is the undo button you have been missing. Changed something, broke it, want it gone? `git restore .` returns every uncommitted file to the last commit. Committed work is untouched and safe.

Commit at every loop step. It is free, it is fast, and the worst mistake you can make costs you a few minutes instead of a day. A remote like GitHub is optional and a later step; local commits give you the full safety net.

### 5.3 pandas and matplotlib: where to start

Do not study the API cold. Open the two example notebooks, read them top to bottom, and watch the same five or six pandas moves repeat: `read_csv`, `.head()`, `.dtypes`, `.isna().sum()`, `.groupby()`, `pd.to_numeric()`. That is most of the daily job. matplotlib is one pattern repeated: make a figure, draw one thing, label it, save it.

Then change one number in a cell and rerun. You learn more in an hour of editing a working notebook than from a week of tutorials. When you hit a real wall, go to the reference shelf in section 11.

---

## 6. The operating loop

Every project runs these seven steps in order. This is the SOP. When you feel lost, find which step you are on.

**1. Question.** Convert the vague brief into one precise, scoped, answerable question, and name what you will measure to answer it. Write both down before you touch data. This is the first section of the notebook: the brief sits in the header cell, the real question and the measure live in section 1, where the reviewer sees them. If you cannot write the question in one sentence, you do not understand the ask yet.

Naming the measure is half of step 1 and the half beginners skip. "Calmer" is not measurable; realized volatility and drawdown are. "Losing units" is not measurable; attainment by shift and a Pareto of downtime reasons are. The measure is the bridge to the data: once you know you are measuring realized volatility, you know you need daily closes, which tells step 2 what to pull.

The brief is rarely the question; it is the noise around the question. Your job is to find the question underneath. Three pointed asks to bring to the requester before you pull data:

- *What decision rides on this number?* If they cannot name one, the brief is fishing, and a fishing brief produces a fishing deliverable.
- *What would change your mind?* This pins what evidence they will accept. If "nothing would," do not start the project; flag it.
- *When do you need the answer, and what does "need" mean?* A wrong number today is worse than a right number tomorrow on a quarterly review, and the reverse on a trade.

If you cannot get those answers, write your best guess for each in the notebook and confirm with the boss before pulling data.

**2. Pull.** Name the specific dataset the measure needs, get it from source into your notebook by code, and record where it came from and when. Plan the pull before you run it; the planning is the work, the code is the easy part. Four things go in the Pull section before you trust a single row:

- *The data.* The specific table, series, or file, named precisely. "S&P 500 daily closes, Jan to Jun 2026," not "the price data."
- *The source.* Where it lives and how you pull it. FRED series SP500, the MES `hourbyhour` table, a CSV in `data/`. A number with no source is a rumor.
- *The pull date.* When you pulled it, in a comment beside the pull code. Data shifts under you; the date is what makes the result reproducible later.
- *Concerns going in.* What could poison the answer before you have seen a row: gaps faked into returns, survivorship, look-ahead, shift boundaries that split a run, downtime reasons logged differently between crews. Section 2 lists these traps by domain. Naming them here tells Profile what to hunt for.

**3. Profile.** Before computing anything, learn what you are holding. Shape, columns, types, missing counts, ranges, head and tail. Read the output like a detective.

**4. Clean.** Fix what profiling exposed. Wrong types, gaps, duplicates, junk rows. Record every decision. Cleaning is where wrong answers are born, so you leave a trail.

**5. Explore.** Look for the answer. Slice, group, plot, compare. Follow what the data shows, not what you expected.

**6. Answer.** State the finding in plain numbers. Pin it to the question from step one. Sanity-check it against reality before you believe it.

"Sanity-check" is a real step with real techniques, not a vibe. Four checks to run on every answer before you trust it:

- *Order of magnitude.* If your number is $1.2M and the line does $30K of value a day, something is wrong by a factor of forty. Find the factor.
- *Sign and direction.* If returns were negative for the period and you reported a positive Sharpe, a sign got flipped somewhere upstream.
- *One row by hand.* Pick a single row of the source, reproduce its contribution to your answer on a calculator. If the calculator and the notebook disagree, the notebook is wrong.
- *External cross-reference.* Does your number match what the MES dashboard, FRED chart, or the client's own report shows for the same window? If not, decide which one is wrong before you ship anything.

**7. Communicate.** One chart, three sentences, source noted. Built so the reader needs nothing else.

Steps 3 and 4 take the most time and get the least glory. That ratio is the job.

---

## 7. Working standards

Non-negotiable. A correct answer produced sloppily is a liability, because nobody can trust the next one.

- **Reproducible by default.** Code in a notebook, data pulled by code, no manual edits you cannot rerun. If you fixed it by hand, it is not fixed.
- **Versioned.** Every project is a git repo. Commit at each loop step with a real message. `cleaned target type coercion` beats `update`.
- **Sourced.** Every dataset records its origin and pull date. Every chart names its source.
- **Documented inline.** Markdown cells explain why, not what. The code shows what.
- **Honest about uncertainty.** When the data cannot answer the question, you say so and explain what would. A clean "we cannot know this from this data" is a real deliverable. Guessing dressed as fact ends the relationship.

---

## 8. Core competencies

The skills the loop and the standards test in you. You build them by doing projects, not by studying them in order. The list exists so you can name your own gaps and bring them to review.

- **SQL.** Pull, filter, join, aggregate, window. (Deferred until your first database project.)
- **pandas.** Load, profile, clean, reshape, merge, group. Your daily surface.
- **Data profiling.** Reading a fresh dataset and knowing within ten minutes what is in it, what is broken, what you cannot trust.
- **Data cleaning.** Missing values, wrong types, duplicates, outliers, silent encoding errors. The unglamorous work that decides whether your answer is real.
- **Exploratory analysis.** Finding the story before you commit to a claim.
- **Applied statistics.** Distributions, correlation versus causation, base rates, when a difference is noise.
- **Visualization.** One chart that makes the point. Not five that bury it.
- **Communication.** Three sentences a busy non-analyst acts on. The hardest skill and the one clients remember.
- **Reproducibility.** Anyone can rerun your work and get your number. Without this you have an opinion, not an analysis.

---

## 9. The daily ritual: power-on to finished paper

The traceable path, every step, the way it runs once you are fluent. Compare it to what you do and refine it.

**Get to your desk:**

1. Power on. Open Windows Terminal.
2. Enter your workspace: `cd C:\Users\you\analyst`
3. Turn the environment on: `.venv\Scripts\Activate.ps1` (prompt shows `(.venv)`)
4. See where you left off: `git status` then `git log --oneline -5`
5. Open the workspace: `jupyter lab`

**Open the project:**

6. Start a notebook for the project in its own folder, named plainly: `01-yield-curve/project-01-yield-curve.ipynb`. One notebook per project.
7. Header cell, before any code, paste the **brief** in markdown, exactly as the requester phrased it. Then in **section 1**, write your **real question** and the **measure** you will use to answer it. That is loop step 1, on the page, where the reviewer sees it.

**Work the loop, top to bottom:**

8. Pull, Profile, Clean, Explore in code cells. After each, a one-line markdown cell saying what you found. The notebook should read like a story someone else could follow.
9. **Set notes to the side.** Keep one markdown cell at the very bottom titled "Notes to the side (scratch)." Every dead end, every TODO, every question for the senior analyst goes there and only there. It never reaches the client. This is how the narrative above it stays clean while you capture the messy thinking.
10. Land the **Answer** in a markdown cell: the finding, plus the sanity check that made you believe it.

**Finalize the paper:**

11. Build the one chart. Save it: `plt.savefig("chart.png", dpi=120, bbox_inches="tight")`.
12. Write the **deliverable** in a markdown cell at the bottom of the work, above the scratch notes: headline, chart, three sentences, source line. This is the only part that leaves the building.
13. **Prove it reproduces.** Menu: Kernel, then Restart Kernel and Run All Cells. If it runs top to bottom with no error, the work is real. If it breaks, you had hidden state. Fix it now, before anyone trusts the number.
14. Save. Close the notebook.

**Save and ship:**

15. Back in the terminal: `git add .` then `git commit -m "project 01: yield-curve read shipped"`.
16. Copy the deliverable out and send it (or hand it to review).

**End of day:**

17. Mid-project, commit a checkpoint: `git commit -m "wip: profiled and cleaned, resume at explore"`, and write the resume point in your scratch notes. Tomorrow you start at step 2.

That is the path from power button to finished paper, with nothing skipped.

---

## 10. Notebook hygiene and the notes discipline

- **One notebook per project.** It reads as a story: question, pull, profile, clean, explore, answer, chart, deliverable, scratch notes.
- **Narrative cells carry the meaning.** A short markdown note after each code step, saying what you found, is what makes the notebook reviewable.
- **Scratch lives in one place.** All dead ends and TODOs go in the single "Notes to the side" cell at the bottom, never mixed into the narrative, never in the deliverable.
- **No graveyards.** Delete dead code instead of commenting it out. git remembers every version; that is its whole job.

---

## 11. The reference shelf

What a working analyst reaches for mid-task. You are not expected to know these cold. You are expected to know where to look.

### 11.1 When you are stuck

Work the escalation in order. Each step takes longer than the last; each is cheaper than the one after it.

1. **Read the error out loud.** The exact message often contains the fix. `KeyError: 'date'` means the column is not named `date`; check `.columns`.
2. **The reference shelf, exact problem.** Open the source below that maps to your wall. Search the problem in the source's own words.
3. **The senior analyst, with what you tried.** Bring the question, the two things you tried, and what each returned. Vague help asks waste both of you; specific ones get answered in a minute.
4. **Log the lesson in scratch notes.** Whatever the answer was, write it where you will find it next time. A working analyst's notes file is their second brain.

### 11.2 The shelf itself

**Stuck on pandas:** *Python for Data Analysis, 3rd ed.* by Wes McKinney (the author of pandas), free at wesmckinney.com/book. The official pandas docs, "User Guide."

**Stuck on SQL (later):** pgexercises.com for practice, the Mode SQL Tutorial for analyst patterns, the PostgreSQL or DuckDB docs for exact behavior.

**Stuck on statistics:** StatQuest (Josh Starmer) on YouTube, *Think Stats* by Allen Downey (free online), seeing-theory.brown.edu for visual intuition.

**Stuck on the chart or the message:** Cole Nussbaumer Knaflic's *Storytelling with Data* blog (free), the matplotlib gallery for "how do I draw this."

**Operations domain ground truth:** Lean Six Sigma green belt material for DMAIC and SPC, ASQ references for OEE and control charts.

**Finance domain ground truth:** the Kenneth French Data Library documentation for what each factor means, FRED itself, where every series carries its own description and source notes.

**Structured backup, if you want it.** You run Boot.dev and a Coursera ML track. Treat those as the gym you visit when a project exposes a fundamental gap, not as the path. The projects drive; the courses patch.

---

## 12. The project format

Everything we do is a Project, in this exact shape, so you always know the target and the bar.

```
PROJECT [NN]: [Title]

BRIEF                          (header cell)
  The vague ask, as a client or boss would phrase it.

THE REAL QUESTION + MEASURE     (section 1)
  Your one precise sentence, plus what you will measure to
  answer it. Loop step 1. I check it before you proceed.

DATA                           (section 2)
  The specific dataset, its source, the pull date, and the
  concerns you are watching for going in.

DELIVERABLE
  Exact format. Usually: one chart, three sentences, sourced.

PROPER COMPLETION  (Definition of Done: correct and usable)
  - The answer is right and survives a sanity check.
  - The work reruns start to finish with no manual steps.
  - The chart and sentences answer the real question.
  - Sources and pull dates are recorded.

GOOD COMPLETION  (Excellence: keeps you employed)
  - You caught something the brief did not ask about and it matters.
  - The code is clean enough to reuse on the next client.
  - You named the limits of the data before being asked.
  - The reader needs zero follow-up questions.

TIME BUDGET
  What this should take an analyst in your seat. (Section 13.)

PITFALLS
  The specific traps in this dataset. Where wrong answers hide.

REVIEW
  I read it against the rubric in section 14 and tell you
  proper, good, or back-to-the-bench.
```

---

## 13. Time budgeting

Estimates for a junior analyst, learning curve included. Going over is fine while you build the loop. The point is to feel the clock, because clients feel it.

| Project type                                     | Target time  |
| ------------------------------------------------ | ------------ |
| Ad-hoc "clean read" (one number, one chart)      | Half a day   |
| Profile and clean a fresh dataset                | 1 to 3 hours |
| Mini-report (question, analysis, chart, writeup) | 1 day        |
| Multi-part analysis across a few datasets        | 2 to 4 days  |
| Build a small reusable tool                      | 3 to 5 days  |

When you blow past a budget, the question is not "am I slow." It is "which step ate the time, and was it Profile and Clean." It usually was. That is normal and where the skill compounds.

---

## 14. Review rubric

I review every shipped project against five dimensions.

| Dimension       | The question I ask                                   |
| --------------- | ---------------------------------------------------- |
| Correctness     | Is the number right, and did you prove it?           |
| Reproducibility | Can I rerun this and get your result?                |
| Clarity         | Would a non-analyst act on this without calling you? |
| Hygiene         | Is the code clean enough to reuse?                   |
| Insight         | Did you see past the literal ask?                    |

**Proper completion** clears Correctness, Reproducibility, and Clarity. That ships to a client.
**Good completion** clears all five. That earns you harder, more interesting work.

**Back to the bench is not a failure grade.** It is the loop telling you a step ran ahead of evidence. The fix is to walk back to the step that broke and rerun from there, with what review surfaced now in scope. Nobody loses points for going back; people lose work for not noticing they should.

---

## 15. Progression

Projects escalate as the loop becomes muscle memory.

- **Stage 1, known answers.** The question has a checkable answer (the yield-curve read is here). You calibrate against reality and learn to trust your own output.
- **Stage 2, open questions.** Real data, no answer key. You defend your conclusion because nobody can confirm it for you.
- **Stage 3, your own data.** We point the loop at data nobody has read, including your PoE snapshots. Now you are doing the job, not practicing it.

Each stage adds one new tool or skill, when a project demands it and not before.

---

## 16. Your example library

Three reference files live beside this manual. Read them before Project 01.

- **example-notebook-operations.ipynb**: a full loop on a Lean operations question (where a production line loses its units). Open it first; it shows every step on the page.
- **example-notebook-finance.ipynb**: the same loop on a quant-finance question (is a benchmark riskier than it feels). Same structure, finance dialect.
- **client-deliverable-reference.md**: what leaves the building, and why it is shaped the way it is. The notebook-versus-deliverable line, made concrete.

Both notebooks reproduce with Restart and Run All, so you can break them, rerun them, and learn from the wreckage safely.
