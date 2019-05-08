# High Level Proposal:
1. Define and publish a process to handle issues in a structured manner.
    1. Issue Handling Process
    1. Issue Stages
    1. Roles
    1. SLA

## Process Definition

### Issue Handling Process
1. All incoming issues should be assessed. These are items that a Triager would look at on a daily basis
1. Every issue triaged should be assessed for its workability. If assessing the workability of the issue requires more information, the Triager would collaborate with the OP until workability is determined.
    1. Workability Classification (also below are label names):
        1. feature-request
        1. enhancement
        1. bug
        1. wontfix
        1. docs
1. Workable Issues should be prioritized and Labeled as one of 3 priorities
    * P0 = Critical (Drop everything and work on it.)
    * P1 = Important (Work on it if no P0 are present. Use judgment to prioritize items within a list of P1s)
    * P2 = Low (Work on it when no P1 are left or some one from community wants to contribute)
1. Prioritized and workablity classified issues  issues should are considered as ready-for-work. This should signal to any contributor willing to help that they can work on this issue.
1. Once a contributor is working on this issue, the issue should be assigned to them. (Assigned issues are considered to be in in-progress stage.)
1. Once work is complete and issue has been resolved it should be labeled as ‘Completed’ and Issue should be closed. 


### Issue Stages
Stage | Meaning | Action | Label
------|---------|--------|-------
triage-me | Ticket is ready for triage. | No classification or priority labels | -
ready-for-work | Ticket triaging has been completed and is up for grabs to be picked up to be worked on by a contributor. | Ticket is labeled based on the workablity classification and one of 3 priorities. |  See workablity classification and priorities section
in-progress | Ticket is actively being worked upon by a contributor. | Contributor who has picked up this ticket shall assign the ticket to themselves. | -
in-review | Implementation work related to the ticket has been completed. Work related to a Review and/or any changes requested as part of the review  is in progress.  | Issues with a PR associated with it will be considered as in ‘In Review’ | -
completed | Work for this issue has been completed and reasonably tested. | No label is needed. Issue is simply closed. | -
blocked | Issue is blocked for one reason or another. Typically where the delay or waiting period is more than 5 days. | Ticket should be labelled ‘Blocked’ | blocked
will-not-fix | Issue is not workable.  | Issue should have comment indicating why it is not fixable. | wontfix


### Roles
* __Triager__: 
    * Person who is triaging the issue by determining its workability. This person is responsible for getting the ticket to one of two labels - 1) Ready for Work 2) Cancelled. They are responsible for triaging  by working with the OP to get additional information as needed and analyzing the issue and adding relevant details/information/guidance that would be helpful to the resolution of the issue.

* __OP__: 
    * Original Poster. This is the person who has opened or posted the issue.

* __Contributor__:
    * Person(s) that are actually doing the work related to the ticket. Once work is done, they work with the Reviewer and the Contributing Reviewers to get feedback implemented and complete the work. They  are responsible for making sure issue status label is up to date (In Progress, In Review, Completed, Blocked, Cancelled)

* __Reviewer__: 
    * Person(s) whose Approval is needed to merge the PR
* __Contributing Reviewer__: 
    * Person who provides feedback on an issue/PR but who Approval is not necessarily needed to merge the PR.

### SLAs
* __Triager__: Daily (Business Days) review of untriaged issues with initial feedback to OP or issue transitioned to ‘Ready to Work’ within 3 days of last activity.
* __Reviewer / Contributing Reviewer__: Provide feedback within 3 days of the last activity on a PR
* __Contributor__: 3 day updates as comments on the PR to indicate activity

