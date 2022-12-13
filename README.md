# [Sponsorname] Contest

Unless otherwise discussed, this repo will be made public after contest completion, sponsor review, judging, and two-week issue mitigation window.

**Contributors to this repo:** prior to report publication, please review the [Agreements & Disclosures](/issues/1) issue.

---

## Contest findings are submitted to this repo

As a sponsor, you have three critical tasks in the contest process:

1. Weigh in on severity.
2. Respond to issues.
3. Share your mitigation of findings.

Let's walk through each of these.

## High and Medium Risk Issues

Please note: because wardens submit issues without seeing each other's submissions, there will always be findings that are duplicates. For all issues labeled `3 (High Risk)` or `2 (Medium Risk)`, these have been pre-sorted for you so that there is only one primary issue open per unique finding. All duplicates have been labeled `duplicate`, linked to a primary issue, and closed.

### Weigh in on severity 

Judges have the ultimate discretion in determining severity of issues, as well as whether/how issues are considered duplicates. However, sponsor input is a significant criteria.

For a detailed breakdown of severity criteria and how to estimate risk, please refer to [the judging criteria in our documentation](https://docs.code4rena.com/roles/wardens/judging-criteria#estimating-risk-tl-dr).

If you disagree with a finding's severity, **leave the severity label intact and add the label** `disagree with severity`, along with a comment indicating your opinion for the judges to review. It is possible for issues to be considered `0 (Non-critical)`.

Feel free to use the `question` label for anything you would like additional C4 input on.

**Please don't change the severity labels;** that's up to the judge's discretion. 

### Respond to issues

Label each open/primary High or Medium risk finding as one of these:

- `sponsor confirmed`, meaning: "Yes, this is a problem and we intend to fix it."
- `sponsor disputed`, meaning either: "We cannot duplicate this issue" or "We disagree that this is an issue at all."
- `sponsor acknowledged`, meaning: "Yes, technically the issue is correct, but we are not going to resolve it for xyz reasons."

(Note: please *don't* use `sponsor disputed` for a finding if you think it should be considered of lower or higher severity. Instead, use the label `disagree with severity` and add comments to recommend a different severity level -- and include your reasoning.)

Add any necessary comments explaining your rationale for your evaluation of the issue. Note that when the repo is public, after all issues are mitigated, wardens will read these comments.

## QA and Gas Reports

For low and non-critical findings (AKA QA), as well as gas optimizations: all warden submissions in these three categories should now be submitted as bulk listings of issues and recommendations: 

- **[QA reports](https://docs.code4rena.com/roles/wardens/judging-criteria#qa-reports-low-non-critical)** should include *all* low severity and non-critical findings, along with a summary statement.
- **[Gas reports](https://docs.code4rena.com/roles/wardens/judging-criteria#gas-reports)** should include *all* gas optimization recommendations, along with a summary statement. 

For QA and Gas reports, we ask that you: 

- Leave a comment for the judge on any reports you consider to be particularly high quality. (These reports will be awarded on a curve.)
- Add the `sponsor disputed` label to any reports that you think should be *completely* disregarded by the judge, i.e. the report contains no valid findings at all.

## Once labelling is complete

When you have finished labelling findings, drop the C4 team a note in your private Discord backroom channel and let us know you've completed the sponsor review process. At this point, we will pass the repo over to the judge to review your feedback while you work on mitigations.  

## Share your mitigation of findings

*Note: this section does not need to be completed in order to finalize judging. You can continue work on mitigations while the judge finalizes their decisions and even beyond that. Ultimately we won't publish the final audit report until you give us the ok.*

For each finding you have confirmed, you will want to mitigate the issue before the contest report is made public.

As you undertake that process, we request that you take the following steps:

1. Within your own GitHub repo, create a pull request for each finding.
2. Link the PR to the issue that it resolves within your contest findings repo.

This will allow for complete transparency in showing the work of mitigating the issues found in the contest. **Do not close the issue;** simply label it as `resolved`. If the issue in question has duplicates, please link to your PR from the open/primary issue.
