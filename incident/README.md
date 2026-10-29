# Incident 2026-10-20: the review queue fails for health plan staff

This packet is what you get before the session: the customer reports, the
handover from the on-call engineer, the incident channel transcript, the api
team's dashboard and the alarm history for the afternoon, and two documents
describing the platform. The
company, the platform, the people and the numbers are all fictional.

You may prepare however you like, including with AI tools. This packet
comes in a GitHub repository we sent you the link to. Until the session
the repository holds the packet and a placeholder for the code, so you can
check that your editor and AI tools can open it. When the session starts we
replace the placeholder with the source code of the platform (the release
that went out today and the release before it). In the session, Tomas, our
platform engineer, is on the call: post in the call chat anything you want
from him (logs, any runtime state you would look at during a real incident,
a command to run, a change to make), and the results arrive in that
repository. You cannot push to it yourself.
Your AI tools are welcome in the room; we want to see how you work with them.

## The ask

At 16:30 UTC you take over the incident. Nothing is resolved. Come ready to
say what you believe is going on and how sure you are, what you would look at
first, and what you would ask for.

**A call with the customer comes first.** Harborview's IT director, Jordan
Reyes, has asked to speak to whoever is leading the incident, and the call is
booked for the start of your shift. It is short (five minutes, right after
the brief). They want to know what is being done and how serious it is.
This call is a required part of the session: prepare how you will talk to
them, given what you know and what you do not know yet.

## Customer reports

**Health plan customers (payer app).** From about 15:30 UTC, reviewers see
"Submissions could not be loaded. The API answered 500." on the review queue.
It comes and goes: the next automatic refresh often works. Since about 16:20
it happens more often. Bluecrest and Harborview phoned; Harborview says
"about one refresh in three fails now". Product has been answering "we are on
it" since 15:40.

**Partners and providers.** No reports.

## Handover from the on-call (Dana, 16:28 UTC)

> **What went out.** Release 2026-10-20, deployed at 13:50 and finished green
> at 14:27. Three changes: a nullable `priority` column (migration 0003,
> filled in batches), a five-second cache in the api for the submissions
> list, and the payer app's review queue now refreshes itself every five
> seconds.
>
> **What I saw.** From 15:02 the api's memory alarm started flapping. The api
> processes are being killed for running out of memory, again and again, and
> come straight back. From about 15:30 a share of api requests fail with 500,
> and it has got worse since.
>
> **My read.** It is the new cache. It should keep each answer for five
> seconds, but memory only goes up. The errors started after the crashes did,
> and they got worse as the crashes kept coming, so I think the crashes are
> what break requests.
>
> **What I did.** 15:52 opened pull request 1093 reverting the cache commit.
> CI is green; it needs an approval and a deploy, about 40 minutes end to end.
> Nothing else changed by hand.
>
> **Current state.** The api is on the new release and healthy in the load
> balancer. Alarms in ALARM: api-5xx. api-memory-high keeps flapping. Partner
> traffic, workers and the web apps look normal.
>
> **What I would do next.** Get the revert approved and deployed, and maybe
> double the api task memory in the meantime so the processes stop dying.

## The status page

Customers follow our public status page, `status.example.test`. It is where
we tell them about outages, degraded service and planned maintenance, and
customers are told to check it before they phone us. A screenshot as it
looked at 16:30 is in `status/status-page-at-1630.png`.

In the session you can post to it at any time: tell us the component, the
status (investigating, identified, monitoring, resolved, or a maintenance
notice) and the exact text, and we publish it as written.

## About logs

Our logs live in CloudWatch, and the log groups have never had a retention
period, so they hold years of data. An export is a Logs Insights query that
scans those groups; a wide query over the whole afternoon takes a long time
and draws on the same account limits as our alarms and the deploy pipeline.
During an incident that is load we cannot afford on production. So we pull
logs for one service at a time, for one window of up to 15 minutes. In the
session, tell Tomas which service, which window, and why.

## What is in this packet

| Path | What it is |
|---|---|
| `architecture.md` | One page on the platform: services, data flow, tenancy, how deploys work, operating numbers. |
| `session-briefing.pdf` | One page: the company, a recent incident, your role, how the session runs and what arrives when. |
| `dashboards/*.png` | The api team's CloudWatch dashboard for 2026-10-20, 12:00 to 16:30 UTC: public load balancer requests and 5xx, api latency and target health, api memory, partner load balancer, Celery queue depth. |
| `alarms/cloudwatch-alarm-history.log` | Every alarm transition of the afternoon. |
| `status/status-page-at-1630.png` | The customer-facing status page at 16:30. |
| `channel/incident-channel.log` | The incident channel transcript, from the deploy to the handover. |

Everything is from the environment `skeleton-prod` in `us-east-1`. Times are UTC.

## In the session

How the 75 minutes run, and what arrives when, is in `session-briefing.pdf`.

Please keep the material private. It is fictional, but it is ours. We delete
the repository right after the session.
