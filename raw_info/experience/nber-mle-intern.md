---
title: Machine Learning Engineer Intern
org: NBer
location: San Francisco, CA
dates: 2026-06 – 2026-08
constant:
  - Headline result: the ~40-line default-policy change outperformed the ranking model (3.1pp vs 0.8pp redemption lift)
  - Production serving stack: Python/gRPC, Redis, Kafka, Docker/Kubernetes — shipped a service in the order path, not a notebook
---

## Detail

Voice notes from the week after I got back. Not cleaned up.

### what it was and why they cared

> nber is rides and delivery, there's a paid membership plan, and everybody on
> it gets the same fixed benefits. the bet we were testing is what if it's
> dynamic — a model ranks which benefits are actually worth something to you, you
> get a push asking you to pick one, and if you don't pick you get a default. and
> at order time, if your benefit applies, the app surfaces it so you don't have
> to remember you have it.
>
> they cared because of churn. people cancel around month three saying "i wasn't
> really using it," and a lot of them *were* getting value, they just didn't know
> what they had. marcus the pm said in kickoff that members order about 3x more
> than non-members, so a point of retention here is worth way more than a point
> anywhere else.
>
> it was a prototype, not a product. two metros, austin and denver, about sixty
> thousand enrolled members, three monthly cycles, team of six.

### what i owned vs what i touched

> i owned the feature pipeline and the ranking model. wrote them, would defend
> every line.
>
> i touched the order-time eligibility check — i wrote the service, priyanka
> wrote the checkout call site because interns don't go in the payments path —
> and the default policy, where i did the analysis and wrote the change but it
> was forty lines and my manager reviewed it in nine minutes. that forty lines
> turned out to be the most important thing i did all summer.

### the pipeline and the bug that ate two weeks

> to rank benefits you need order history, membership tier, city pricing, and
> which merchants near you are partnered. four systems, four freshness
> guarantees. order history is nightly out of the warehouse, tier is a live
> service, city pricing is a yaml file in a repo, and merchant partnership status
> updates whenever someone on partnerships remembers to run the job.
>
> model looked great offline, ndcg@3 from 0.61 on the popularity baseline to
> 0.74. then cycle one ran and online was completely flat.
>
> training serving skew. i trained on end-of-month features, a full month of
> behavior. but we score at the *start* of a cycle, when the nightly batch has
> only gotten through part of the month. "orders in the last 30 days" meant
> something different in training than in production, and for recently joined
> members it was drastically different.
>
> how i found it wasn't clever — dumped feature vectors from both paths for the
> same hundred members and diffed them by hand in a notebook. should have done
> that day one; instead i spent a week and a half re-reading model code that was
> never the problem. the fix dropped offline ndcg to 0.68, which is lower and
> correct, and i had to explain in standup why my number went down and that this
> was good news.

### the model

> lightgbm ranker, not a neural anything — sixty thousand members and nine
> benefits is not a deep learning problem and reaching for a transformer would
> have been showing off.
>
> cold start was real. someone who enrolled two weeks ago has almost no signal
> and those are exactly the members churn happens to, so i fell back to
> city-plus-tier population priors under 10 orders of history. epsilon greedy at
> 10 percent for exploration, otherwise it recommends free delivery to everyone
> forever and we learn nothing about the other eight benefits. my manager asked
> why 10 and the honest answer is it's the number people pick.

### the forty lines

> push open rate was about 26 percent and about three quarters of openers picked
> something, so overall selection was around 19 percent. which means 81 percent
> of members never chose and got the default. the default was deciding the
> benefit for four out of five people and my model was operating on the other
> one in five.
>
> and the default was "most redeemed benefit across the whole program" — free
> delivery, everybody, both cities. i changed it to most redeemed within your
> city and your usage tier. that's a group by. forty lines with tests.
>
> overall redemption went up 3.1 points. everything my ranking model did was
> worth about 0.8 points overall, because it only touches 19 percent of people.
>
> eight weeks on the model, two days on the default. i learned enormously more
> from the model and contributed much more with the default.

### the thing that bothered me the whole time

> the model got very good at recommending the benefit you were most likely to
> use, which is usually the one you were already getting value from. free
> delivery ranks first for people who already order four times a week. they
> redeem it, redemption goes up, everyone claps. but did we change anything?
>
> nobody had decided whether the goal was redemption or incremental orders, and
> those want completely different models. i raised it in week seven, it didn't
> get resolved, and it's in the team's open questions doc with my name on it.

### the cost nobody owned

> redemption went up and notification opt-out also went up, about 0.4 points
> versus control. opting out is basically permanent and it affects every team
> that wants to reach that user. our metric went up and we spent a shared
> resource that wasn't on our dashboard. i flagged it; shipping anyway wasn't my
> call.

### who else was actually in this

> from the inside it felt like i was alone with a notebook. i wasn't.
>
> - **data platform** owned snowflake and airflow. a new dag and a service
>   account was a ticket and a conversation, not self-serve.
> - **partnerships** owned the merchant table. i found the analyst who owns it
>   and asked how often it really refreshes — "weekly-ish, more like biweekly in
>   december." that conversation is the only reason my freshness handling is
>   right, and i'd never have gotten it from the docs.
> - **payments eng**, priyanka, for the checkout integration; we pair debugged
>   timeout behavior for an afternoon.
> - **crm / lifecycle** owned push and i understood them least at first. they own
>   the notification budget for the entire company, so our monthly push competes
>   with everyone's campaigns and i had to go present to them for a slot. they
>   also own the opt-out metric, which is how i knew to look at it.
> - **design** did the picker panel. i had opinions about three options versus
>   five, design had data from a prior study, i was wrong.
> - i shadowed a **support** shift, which nobody asked me to do. best day of the
>   summer for understanding the actual problem.

### things that were mine to start

> the uplift question — i asked marcus and the crm lead for thirty minutes and
> walked them through why redemption might be the wrong metric. i made the case
> okay and not great; i had one chart, and what would have landed was a worked
> example with two hypothetical users.
>
> the default change — nobody assigned it, i noticed the 81 percent while
> building an unrelated chart and chased it. and i wrote a runbook for my
> pipeline, because i inherited one without a runbook and it cost me days.
>
> where i got overruled: i wanted the push every six weeks instead of monthly
> given the opt-out movement. crm had historical data, i had one pilot. they were
> probably right and i was still right to say it.

### stack

> python 3.11 and pandas on an airflow dag, daily, upstream in snowflake with
> some dbt models owned by data platform. wrote the pipeline in scala spark first
> because that's what everything else was, then threw it out and rewrote in
> pandas — sixty thousand members is not a spark problem and iteration speed
> mattered more than runtime.
>
> lightgbm LGBMRanker with lambdarank, sklearn for metrics and calibration,
> mlflow tracking every run which saved me during the skew thing. feature
> snapshots per cycle as parquet on s3 so a run is reproducible. validation was
> assertions plus pytest.
>
> eligibility service is python behind grpc with a protobuf contract, redis
> cache, kafka consumer for invalidation, docker image running on the cluster
> the platform team operates, deployed through helm. load testing with locust,
> opentelemetry traces into datadog dashboards. a/b through their internal
> platform, everyone calls it beaker. jupyter and seaborn for analysis.

### the eligibility service

> the order-time check is a small python service behind grpc, contract in
> protobuf so the checkout side and i could version the schema independently
> when i added a field mid-pilot. redis in front, fifteen minute ttl. i wrote
> the service, priyanka wrote the call site.
>
> the part that took actual thinking was cache invalidation. if your membership
> tier changes, your eligible benefits change, and the tier service publishes
> those changes to kafka. so i consume that topic and evict the redis key.
>
> priyanka asked why the fifteen minute ttl wasn't enough on its own, which is
> a fair question. the answer is that this one sits in checkout. if you upgrade
> your tier and order right away, showing you a benefit that then doesn't apply
> is a bug you actually see. fifteen minutes of stale is fine on the batch side
> and not fine here.
>
> kafka is at-least-once, so you sometimes get the same change twice — which is
> harmless for an evict, but i had to convince myself of that rather than assume
> it, and that's the first time i've had to reason about delivery semantics for
> something real rather than for a problem set.
>
> containerized it, wrote the helm values, deployed onto the kubernetes cluster
> the platform team runs. i did not operate that cluster and wouldn't claim i
> did.
>
> load tested with locust before the pilot and p99 blew way past the 30ms budget
> at about 3x expected qps. it was connection pooling — i was opening a new redis
> connection per request. embarrassing, two line fix. that test is also why the
> thing fails open: if the service is slow or down, checkout shows no benefit
> rather than blocking the order. nobody loses a sale because my service is sad.

### misc

> - three weeks of onboarding, two of them access requests, spent convinced i was
>   failing. apparently universal.
> - priyanka made me rehearse the readout twice — twenty people, twelve minutes.
>   both rounds of notes were the same note she gives on all my writing: cut the
>   hedging. i open every sentence with "it seems like it may be the case that."
> - return offer for next summer, moot since i graduate.

### numbers

> - 2 metros, ~60,000 enrolled members, 3 monthly cycles, 9 benefits
> - ~400,000 orders/month across the pilot population
> - push open ~26%, overall selection ~19%
> - ndcg@3: 0.61 baseline → 0.74 (skewed) → 0.68 (correct)
> - ranking model: ~0.8pp overall redemption lift
> - default policy change: ~3.1pp lift, ~40 lines
> - notification opt-out: +0.4pp vs control
> - order-time eligibility check: 30ms p99 budget, fail-open
> - load test surfaced p99 regression at ~3x expected qps (connection pooling)
> - team of 6
