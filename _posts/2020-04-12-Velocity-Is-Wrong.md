---
layout: post
title: Velocity Is Not What You Think It Is, you should stop using it.
---

Velocity is a piece of information that is not only used, but heavily relied on as a part of scrum and agile processes.  It is used to find underperformers, and measure increases in productivity.  But Velocity is not only unreliable, even when it is relatively trsutworthy it is not a metric that should be used.  Velocity on a scrum or agile team is a bellwether; no more and no less.  I want to talk about it:

## Task Planning is an Art Form

Most systems that measure velocity require a rating system on any tasks measured.  This means that any task given to a developer should have a complexity rating given to it, and it is broken down enough ahead-of-time to fit into the sprint or development window. It might be useful if all of these things are correct and guaranteed:

1. The task is completely understood beforehand and documented completely.
1. The complexity rating is correct for all potential members of the team.
1. Absolutely zero additional tasking or scope creep from management.
1. Absolutely zero technical debt present in the system requiring rework.
1. Absolutely no inter-team dependencies or scheduled dependencies.
1. All team members are perfectly healthy - physically and mentally - and performing at 100%.
1. No problems will arise from the team members equipment.
1. No problems will arise from important external services.

That is not a complete list, but it's long enough for me to safely say: "That is never the case".

## Point Estimation is Inaccurate

Tasks that are planned in a scrum or agile team are assigned complexity points.  This can vary from team to team, and can represent measurable things such as hours or days.  Task points can also be arbitrary "complexity" points.  I say arbitrary, as once you move from a concrete to an abstract basis of measurement, each team members perception of that metric will be different.

These points can be estimated by a lead developer, by the whole team, by the developer assigned the task, or a combination of these.  The teams that I have been on were either estimated by the entire team and averaged, or estimated by the PO, technical lead, and the assigned developer.

What this means here, is that if we imagine that the requirements and guarantees of the "Task Planning is an Art Form" section are true and guaranteed, we are still using estimated values based on team members' perception of the difficulty of a given task.  Based on members differences, specialties, and experience levels, this guess can be wildly innacurate.

## Velocity is a Derived Metric

So we come to measuring velocity.  What is velocity in this context?  From [Scrum Inc.](https://www.scruminc.com/velocity/):

>Velocity is a measure of the amount of work a Team can tackle during a single Sprint and is the key metric in Scrum. Velocity is calculated at the end of the Sprint by totaling the Points for all fully completed User Stories.

So, velocity is derived from the assigned point values on all assigned work that has been completed.  This is pretty complex given where the points come from, so I with create a workflow charicature here to illustrate the complexity:

1. The planning members of the team (The Product Owner) have a perfect understanding of all requirements.
1. Requirements are solicited and fleshed out entirely, and documented correctly.
1. The Technical Lead and Product Owner derive all internal and derivative requirements correctly.
1. The derivative requirements are fleshed out entirely, and documented correctly.
1. The requirements are reasonable, and accomplishable within the timeframe.
1. The work break-out by the Technical Lead and any senior engineers is comprehensive, complete, and documentated correctly.
1. Dependencies and schedulable interactions are solved.
1. Point estimation is accurate, guaging the difficulty or time on a task for a specific team member.
1. The sprint is stacked allowing appropriate overhead for team members to attend meetings, give or receive mentorship, and increase their own core compentencies.
1. The sprint is executed correctly, without any rescheduling, restacking, or replanning occurring.
1. All planned work is completed.
1. The velocity of that particular sprint, planning, stacking, and team members, is dependable.

Thats a lot of garbage, obviously.  This is a way to demonstrate that complexity estimations in an imperfect system, and with imperfect teams, planning, and interactions with other teams and dependencies, is unreliable.   Some velocity measurements try to include more information, such as number of git commits, or types of changes to a codebase, to get a better picture of whats going on.  Those are very context, and team member workflow specific, and will change depending on tasking, experience, or current problem approach, and are therefore unreliable too.  I don't think that is a surprise to anyone, is it? 

So, what is the problem then?

## Velocity is _not_ a Team Member Performance Metric

I cannot overstate this, using an unreliable derived measurement, that is affected by almost every person in the command chain, planning process, and even day-to-day operations of a company, should not be used to measure the effectiveness of a team member.  Velocity measurement is a (bellwether)[https://www.lexico.com/en/definition/bellwether] on the entire software development process as it is used by a company, not an indicator that any particular member is over - or under - performing.

A change in velocity is an indicator of the performance of the entire process at the company, and can only be compared to its-self within the context.  A drop in velocity could be as simple as more people are sick or stressed, or can be a flawed process within the organization.  I have seen first-hand when it is misconstrued to be more than that.

## Company Oversight and Velocity

A company that is concerned about its future will want to know how well it is doing.  Unfortunately, fields such as Digital Artist, Software Engineering, IT, and even management are more art form than science.  Even trying to measure success with deliverables is difficult, as there is no measure as to whether a required deliverable had an appropriate timeline assigned to it.  And there lies the problem.  Agile and Scrum based development comes with promises of increased velocity, and even provides a mechanism to "measure" that velocity.  As you have read here, there are a lot of caveats to a measured velocity, but its a nice understandable number, and bigger means better, so its a low-hanging fruit for any organization looking to understand its-self.  It provides a blanket of what appears to be hard measurement data on how the company is doing.

What happens inside a company that is using a "hard" metric for success, when a person wants to appear successful?  They will play to that metric, of course.  If a system looks at git commits as a measurement of success, team members will break work into smaller commits.  If a system relies on story points, a team member may try to inflate story points to reduce their workload while maintaining the appearance of productivity.  But these are team members, and should be held in check by management, right?

What happens when the members of management want to look good in this system?

## Bad Managers and Velocity

A bad manager is willing to sacrifice the well-being of their team to make themselves look better.  They can be less-concerned with the growth of people under them, and focus instead on their velocity.  When this happens, thre are a few common scenarios that can play out:

1. Speed of development is prioritized over quality of development.
1. technical debt becomes allowable to reach delivery goals.
1. Team members that are lowering the teams velocity, or their "personal velocity" is not satisfactory can be put on probation for "non-performance".
1. Teams can be reprimanded for "non-performance".
1. team membrs face harsh scrutiny and pressure to increase their performance and velocity, to the detriment of their own growth, and health.
1. Team members can be promoted for "high performance" without respect to any mentorship or quality standards.
1. Teams and team-members can be (directly or indirectly) sabotaged as this "high-performance" but low-quality code enters their tasking.

This is hardly fair, and is very detrimental to Teams, team-members, and companies where this happens.  This happens in metrics driven companies, even successful ones like (google)[https://www.businessinsider.com/google-employees-worst-things-about-working-at-google-2016-12].  teams or team members will be misattributed with success, and the systematic failures and/or the failures of other members can readily be misattributed into a "poorly performing" team member.

## What can be done?

So what can we do, if even the mightly Google can be hamstrung by its own metrics?  In my experience, here are a few things to start:

1. Team members, even lowly, Junior, or Lone Ones, are people deserving of respect.
1. Burnout is real, and experienced, and useful Human Capital can be hard to find.
1. Crunch time, involving nights, weekends, and overwork (Anything beyond a plain 40/hr week) should be extremely rare, and represents a failure in leadership.
1. Low velocity is a company+team metric.  Nobody should be hired, fired, or pressured because of it.  Root cause analysis should be applied for real improvement.
1. High velocity is not an indicator of success.  Work getting done too quickly is just as bad as work getting done too slowly.
1. Teams should be grown and improved, and not reduced to a metric.

This reduces into respect, mindfullness, assertiveness, and not reducing real people with real effort into a performance metric.  If you establish and grow a good team, it will perform.  If you try to force performance while neglecting your team, things can happen, and a lot of them are bad.

I think those things are obvious too, but it seems rare to see any of them applied, even academically.  Instead I see stories of rediculous hours, and abuse of workers at software companies.  I see shoddy work presented as "the future", while maintenance and support be damned.  Reliance on velocity as the tell-all of success and productivity is not reliable or useful enough to outweigh its tradeoffs, and is not able to indicate the health or means of any single team nor team member.

What does your company prioritize?

## What is Better?

If velocity is not a good metric of team and individual productivity, how do we measure individual productivity?  You can't, at least not with automated metrics nor online services.  You have to talk to your people, directly and regularly.  You must enable and support your team members, and stop extracting value from them.  There is not be a one-size-fits-all solution, just as there is not a magical metric that when optimized grants your company success.  We are all people, and as social animals, we cannot be locked into metrics or lanes and be expected to perform.  Respect your people, speak with your people, and excersize leadership as guiding from the front, instead of driving from behind.