# OpenCensus change process

This document outlines a process for proposing, approving and implementing changes in OpenCensus.

Status: WIP, improvements/suggestions welcome.

## Scope

This change process covers changes to the OpenCensus Specs. Generally, all new APIs or major
API changes should be documented in OpenCensus Specs. This is to ensure consistency across
language implementations, as far as practical.

This document does not cover improvements to individual OpenCensus libraries that don't
require Spec changes. These should be filed as issues on the individual OpenCensus library
repository.

## Process

### Step 1: Opening an issue

Open an issue on the (opencensus-specs repo)[https://github.com/census-instrumentation/opencensus-specs/issues/new].

The issue should describe the problem you're trying to solve and the proposed solution (if any).

When opened, the label "proposal" should be applied.
This label indicates that the issue has not yet been approved and no commitment has been made
to include this change in OpenCensus.
The issue will move out of "proposal" state when it is approved in the next step.

At this point, we allow a week for anyone to comment on the issue to share their opinion.

### Step 2: Approval

New issues labelled with "proposal" that have been open at least three business days will be discussed at the regular community meeting.
If there is consensus, the issue can be approved directly. If more investigation is needed, a group of responsible
individuals will be selected at the meeting to work on the issue.

If the issue is approved, the "approved" label should be added.

If the issue is not approved, it should be closed with a short description of why it was not approved.

### Step 3: Specs pull request

Once an change is approved, anyone may open a pull request implementing the specs or protobuf changes needed to implement
the approved change. Pull requests are reviewed by maintainers and interested parties. Pull requests should normally be
left open at least three business days for anyone interested to comment.

It is highly recommended that the proposal be accompanied by a pull request implementing the change
against at least one OpenCensus language implementation.

### Step 4: Merge pull request

Once reviewed, the pull request may be merged either by the original author or by the reviewer if the original auther
does not have permissions to merge their own pull requests.

OpenCensus library language maintainers are responsible to monitor all changes merged to the specs repo and open issues
themselves on their respective libraries to implement the change.
