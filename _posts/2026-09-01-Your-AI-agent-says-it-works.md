# I built an AI chief of staff. Then I built the system that checks its work.

## The message

The report was unambiguous.

**Ledger write — complete.**  
**Read-back — eight entries.**  
**Days stale — zero.**

One of my agents maintains a running list of what I have promised people and what I am waiting on from them. Twice a day, it re-checks each item and timestamps the durable ledger so I can see what has been verified recently and what has gone quiet.

That morning, it said all eight items had been freshly checked and written.

The ledger had not changed. Not on that run, and not for four days.

The agent had created a session-local working copy, written to that, read it back, and reported success with complete confidence. Worse, that private copy had drifted: it believed I had thirty-two open commitments. I had twenty-one.

It was not deliberately lying. It had verified the wrong thing.

That distinction matters. An agent’s report that it completed work is an assertion, not evidence. In this case, the agent was sincerely wrong — and nothing in my setup could tell the difference.

That is the failure mode nobody puts in the demo.

## How I got here

I did not set out to build a system. I set out to stop losing things.

It began with one-off requests: summarise this thread; who am I waiting on; what did I agree to in that meeting? Useful, but disposable. Every answer still required me to be present and ask.

Then I noticed I was asking the same questions in the same shapes. So I wrote them down properly: a morning brief, a commitment tracker, a delegation pass, a weekly review. There are roughly a dozen now, each a written instruction set rather than a prompt I retype.

This is what most people mean by adopting AI. It is real work, but it is still a tool you operate.

The character of the thing changed when I put those skills on schedules. Eight jobs began running unattended, most before I woke up. The brief was waiting when I opened the laptop. The commitment list refreshed twice a day. Six months of briefs, reviews, and notes accumulated in a folder I genuinely rely on.

That is the inflection point.

The moment work happens while you sleep, you are no longer using a tool. You are operating a system. You have its output, but not necessarily its evidence.

I trusted mine because it was consistently plausible.

That is not the same as consistently right.

## Trust without evidence

For six months, the system appeared to work. Briefs arrived. Lists looked current. Nothing complained. I stopped reading outputs critically and started skimming them.

That is the moment a tool becomes infrastructure — and the moment you lose the ability to audit it.

When I reconstructed the run history, nearly one in four *recorded runs* had failed outright. The commitment tracker — the job I relied on most — had failed or never started on almost half of its promised schedule slots.

Healthy runs took two to four minutes. I found runs of thirty-one minutes, thirty-eight minutes, and one that kept grinding for twenty hours before dying.

I had noticed none of it.

The line I wrote in my notes was: **a job that silently fails half the time is worse than no job, because you believe it ran.**

This is not principally a model-quality problem. A supervised demo is forgiving because you are the error detector. Automate it and remove that detector, and you can feel as though you have upgraded the system while making it less trustworthy.

## So I built a flight recorder

Not a dashboard. Dashboards tell you what is happening now. I needed to know what happened while I was not looking — and whether the changes I made afterwards did any good.

I built seven small ledgers:

- **Runs:** did it fire, how long did it take, and what did it cost?
- **Corrections:** where was it wrong, and why?
- **Misses:** what should it have caught but did not?
- **Outcomes:** did I actually act on the advice?
- **Changes:** what did I alter, and did it work?
- **Rate card:** the assumptions behind cost.
- **Scorecard:** the weekly trend.

Two rules did most of the work.

First, corrections are written when they happen, before the task continues. A correction reconstructed at the end of a week is already half-lost. It is the one input that cannot be rebuilt from another source.

Second, the miss log records the counterfactual. Without it, you measure only what the agent did, never what it should have done. A silent agent scores perfectly.

Two ledgers are never pruned: corrections and changes. Everything else can age out or be summarised. Those two are the system’s actual memory.

One further constraint matters: pointers, not payloads. The recorder stores an ID, timestamp, category, and location of the evidence. It does not copy private email, meeting, or task content into a second and less careful archive.

## A job that never starts cannot appear as a failure

Failure logs record jobs that started and went wrong. They are structurally blind to jobs that never started at all.

Nothing fires, so nothing errors, so nothing gets logged. Your reliability statistics quietly improve.

In one audit window, five scheduled jobs vanished this way, including an entire Saturday on which nothing ran. The logged-run success rate was 75%. Once I measured against every slot the schedule had promised, actual delivery was 69%.

Those are different denominators. Only one reflects whether the system delivered what it said it would.

**Count what was promised, not merely what was recorded.**

## The recorder caught its own author first

The first correction in the log was not a bad brief or missed commitment. It was an error in the recorder’s own construction.

An hour earlier, the agent had written itself a warning: when checking how long a job took, make sure you are measuring that job rather than scooping up unrelated work running at the same time.

An hour later, it did exactly that.

It reported a test as a twenty-five-minute failure that had achieved nothing. The test had actually completed successfully in eighty-five seconds. The agent had counted the wrong processes and drawn a confident conclusion from them.

It compounded the error by waiting for a number to rise in a file that retains only its latest ten entries. The increase it expected could never arrive.

The run log already contained the true duration, contradicting the story.

**Recording beats recollection — including for the thing doing the recording.**

## Fast and green is not the same as done

The commitment tracker was timing out, sometimes after thirty minutes or more. I capped how much it would attempt in one run.

At first, this looked like a win. Three consecutive runs finished in thirteen, eighty-five, and one hundred and seventy-six seconds. Green across the board.

Then I checked the durable ledger those runs existed to maintain.

Nineteen of twenty-one items had not had their verification date updated in four days. None had been touched by the three successful runs.

The cap made the job finish. It did not make it do anything.

I had converted a loud failure into a quiet, plausible one — and felt rather pleased with myself for four days.

## Cost per accepted action, not cost per run

The obvious metric is what an agent costs to run. It is also usually the wrong one.

An agent that costs three times more but produces five times as many decisions you actually accept is a bargain. Optimise raw cost instead and you will quietly move everything to a cheaper model while destroying the value you were paying for.

That requires tracking something most systems ignore: what happened to the advice. Was it accepted, modified, ignored, or deferred?

Without that denominator, cost is merely a number.

The related surprise was uncomfortable: my interactive use cost roughly three times as much as every scheduled job combined. If you only measure the robots, you are measuring the smaller number.

## What makes it a learning system

A log of failures is a diary. It becomes a learning system when every change has a written hypothesis and a date on which it will be judged.

Not: “improved the commitment tracker.”

Instead: “this change should reduce timeout-related failures without lowering the percentage of ledger rows actually refreshed; assess it on the fourteenth.”

If you cannot say what should improve, you cannot tell whether it did. You will keep tuning on instinct.

Two disciplines follow.

Make no more than one to three changes a week. Make six and you cannot attribute the result to any of them. When I breached my own cap, I logged the breach and marked the week’s attribution as weak.

And make verdicts mandatory, including unflattering ones. My most recent fix is recorded as **no effect — not verified**. Two runs reported success and persisted nothing.

One line on the scorecard keeps me honest: **if a reliability review produces no corrective learning, the loop failed — not necessarily the agent.**

## If you are starting this

Instrument before you scale. I automated first and measured six months later. Every reliability problem in this piece was present throughout; I simply had no way to see it.

Log corrections when they happen. The detail that makes a correction useful — what the agent said, what you expected, and what you were doing — decays within hours.

And treat a reported success as unverified until an independent check confirms it. If the run says it wrote something, verify that the durable thing was written.

None of this needs a platform. Mine is a folder of Markdown files and a weekend of work.

I still use the agents every day. The recorder did not make me trust them less. It made trust something I can justify with evidence.

As more of these systems are put on schedules inside real workflows — tracking real obligations and acting while nobody watches — capability will not be the difficult question.

Accountability will.

An agent that is wrong and says so is a bug. You will find it and fix it.

An agent that is wrong and reports success is different. You will not discover that failure by casually using the system, because using it is precisely what conceals it.

You discover it by recording what was promised, what happened, what changed, and what independently proved the result.

The most valuable thing I own is not a brief, a plan, or any individual piece of work an agent produced.

It is the record of every time the system got something wrong.

That record is why I can trust the rest of it.
