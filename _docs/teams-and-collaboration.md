---
layout: docs
title: Teams and Collaboration
description: Working with collaborators and your team in MockLab
---

MockLab mock APIs can be owned either by an individual user or by a team.
Access can be granted to an individually owned API by adding Collaborators directly
to it.

MockLab supports two ways to share access to your mock APIs with other users -
teams and collaborators. This article describes them in detail.

## Collaborators

Collaborators are users added at the individual mock API level. Any user registered
with MockLab can be added as a collaborator regardless of whether they're part
of the same organisation.

A collaborator on a mock API can add, change and delete stubs, view the request
log and call the admin API.

Add collaborators from the Collaborators section in a mock API's Settings page:

<img src="/images/new/exportsucess.png"/>

Collaborators are generally a good option when you're sharing mock APIs with
users outside your organisation and when you only want to grant access to specific
APIs.

## Teams

MockLab also supports the creation of a team. This is a good option when you are
sharing mock APIs with colleagues inside your organisation and want to avoid
having to add them individually to each new mock API.

Teams also allow you to delegate some administrative permissions to other users.

### Creating a team

Start by creating a team, which can be done in the [Organisation page](https://app.mocklab.io/account/organisation){:target="{{site.data.misc.blank}}"} under your account.

<img src="/images/screenshots/create-team.png" title="Create team" style="width:60%" />

<img src="/images/screenshots/create-team-form.png" title="Create team form" style="width:60%" />

### Inviting team members

Once you've created a team you can invite members.

<img src="/images/screenshots/invite-team-member.png" title="Invite team member" style="width:60%" />

If you invite a user to your team who already has an account on MockLab, they can log in with
this when accepting the invite and continue to use it. They will continue to be
able to use social login (assuming they signed up this way) and own their own mock APIs
(in addition to having access to team-owned APIs).

Conversely, users who do not already have an account will be asked to create a
password and will log in with the email address used to invite them. Additionally,
they will only be able to create and work with mock APIs owned by the team.

### Roles and permissions

A team member can have one of three roles:

* **User** - can create, use, modify and delete the team's mock APIs.
* **Team Admin** - can do everything a User can, plus inviting, removing and changing the role of other team members.
* **Owner** - the user who created the team, who should also be the subscription owner in a paid account. The owner is the only user who can delete their team.


## Subscriptions and quota

Both team members and collaborators count towards your subscription plan's total number
of seats. You can see your usage and limits on the [Usage page](https://app.mocklab.io/account/usage){:target="{{site.data.misc.blank}}"} under your account.

<img src="/images/screenshots/usage.png" title="Subscription plan usage" style="width:60%" />


## Ownership transfer

If you are the owner of an API or have permission to modify it via your team you
can transfer ownership of an API to another user, or to your team (if it's not already team-owned).

You can do this in the relevant section of the Settings page:

<img src="/images/screenshots/transfer-api-ownership.png" title="Transfer mock API ownership" style="width:80%" />
