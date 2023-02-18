---
author: isotopp
title: "Service Directories, and what they are good for"
date: 2023-02-06 06:07:08Z
feature-img: assets/img/background/schloss.jpg
tags:
- lang_en
- devops
- security
---

At a previous job we had a home-grown application "Service Directory", which allowed a team to declare a service or deployed application.
The record for a service not only declared the application with pointers to the source code repository, artifacts and documentation.

It also pointed to the operational facts, such as criticality, the owning teams, the SLO, the alerts, and the collected stats and dashboards.
And, most importantly, you had to declare dependencies -- which other services you depend on.

# Using Dependencies for Firewalls and Passwords

From data in service directory firewall rules were being generated, automatically.
That meant that with a non-existent or incomplete SD declaration you could talk to nothing and nobody.

Also, all machine accounts (non-personal accounts, NPAs) were provisioned with Hashicorp Vault, controlled by SD.
The data in vault was autogenerated, based on – you guessed it – data from the service directory.
And it was rotated.

That is, every week or so new accounts with new passwords were being added to Vault, picked up by the services, and then also picked up by the clients – order matters a lot here.

Having individualized machine accounts per application helps a lot to identify service (or database) abuse.
It also allows for individualized rate limits or other controls, and of course minimal permissioning of applications.

Monitoring would see if old accounts from last week were still being used.
If so, somebody would need to talk to the team owning the service using these accounts, because obviously something was caching credentials somehow, when it should not.
If old accounts are quiet and stay so for some time, the old accounts can be decommissioned and deleted.

This also makes some nice graphs showing the speed of account (and password) rotation across the enterprise, and these things come handy in audits.
It allows you to prove that passwords are fresh, and that old NPA secret in the hands of leavers cannot be leveraged to gain access to production.

# Using Dependencies for Capacity and Business Continuity planning

Having dependency data also comes in handy in many other tasks, such as (with additional data) planning capacity.
While SD data does not directly contain the volumes needed to plan, dependencies show who can generate demand on a service, and from that it is clear what to measure and where to pick up additional information, such as current fleet size, plans for the coming season and so on.

The dependency web can also be mapped on locations and structural failure boundaries from the underlying physical machinery, and thus it becomes possible to calculate failure scenarios, and using actual placement data, detect co-failure scenarios or write scheduler plugins to avoid them.
Also, mapping dependencies into a graph allows for a functioning black start plan.
It would still require testing, but at least the data for such a thing is there.

# Dependency Inflation

In such a setup, having not enough dependencies will make an application not work.
Thus, SD data can be relied on to be at least as large as the minimum dependencies, or larger.

The "or larger" part is undesirable, but hard to  limit.
The only workable way I have seen requires annoying recurring audits, or equally nagging regular re-requests for all resources.

Experimentally, requested resources and observed resource usage in production can be mapped on each other, and unused resources can be detected.
They cannot be automatically cancelled, because they still may be necessary, if only in rare situations.
But this would at least bring the nag-level down to manageable sizes.