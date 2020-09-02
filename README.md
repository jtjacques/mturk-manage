# MTurk Manage
mturk-manage.html reproduces some of the basic functionality offered by the *Mange HITs individually* option [removed from the Amazon Mechanical Turk requester interface in December 2017](https://blog.mturk.com/upcoming-changes-to-the-mturk-requester-website-and-questionform-data-format-f7c3238be58c).

**This tool is offered without warranty.**

The tool broadly replicates the functionality and layout of the discontinued interface, and is designed to be familiar to those who have previous experience managing HITs.

**There is nothing to install.** The interface runs *locally* in the browser, and has only three external dependencies, the [Amazon AWS SDK](https://aws.amazon.com/sdk-for-browser/), [jQuery and jQuery UI](https://code.jquery.com/), and [AlaSQL](http://alasql.org/), which are loaded automatically.

## Contents

* [Security](#security)
* [Usage](#usage)
    * [Login](#login)
        * [Identity and Access Management](#identity-and-access-management-iam)
    * [Manage HITs](#manage-hits)
        * [Finding & Viewing](#finding--viewing-tasks)
        * [Multi-HIT Actions](#multi-hit-actions)
        * [HIT Status](#hit-status)
        * [Task Assignments](#task-assignments)
        * [Summary Data](#summary-data)
    * [Manage Assignments](#manage-assignments)
        * [Task Level Actions](#task-level-actions)
        * [Assignment Level Actions](#assignment-level-actions)
    * [Other Actions](#other-actions)
        * [Message Worker](#message-worker)
        * [One-off Payment](#one-off-payment)
        * [Manage Qualifications](#manage-qualifications)
        * [Visit mturk.com](#visit-mturk.com)
    * [Known Issues](#known-issues)
    * [Limitations](#limitations)
* [Development](#development)
    * [Change Log](#change-log)
    * [TODOs](#todos)
    * [Acknowledgements](#acknowledgements)
    * [Contact](#contact)
    * [Licenses](#licenses)


# Security

This tool uses a small number of carefully chosen third-party libraries, which are loaded automatically. To minimise the security implications of automatically downloading and including third-party code, all external dependencies include an `SHA-512` `integrity` property to allow the browser to ensure they have not been tampered with.

To minimise the risk of your credentials being leaked, it is recommended that this tool is used with a browser that supports the [Subresource Integrity](https://www.w3.org/TR/SRI/) mechanism. The integrity of external resources is currently validated by [most major browsers](https://caniuse.com/#feat=subresource-integrity).

For security reasons, using copies hosted on a web-server is **not** recommended as they may be subject to tampering and increase the likelihood of your security credentials being intercepted and stolen.

**While the management console does not store credentials, or send them to any third-party, you accept all liability for the security of your access credentials when using them with this tool.**

Additional guidance on limiting the access of your credentials is offered in the *Login* section.

# Usage
Download [mturk-manage.html](https://github.com/jtjacques/mturk-manage/releases) to your local file storage, and open the file in your browser.

The interface runs directly on your computer and untrusted copies, including copies hosted on web-servers, should not be used.

## Login
The login page allows you to enter your API key and API secret as used to create and manage tasks. You will need to enter your AWS *access key ID* and *secret access key*. e.g.

* Sample access key ID: AKIAIOSFODNN7EXAMPLE
* Sample secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

*These credentials are examples only, reproduced from the [Amazon Mechanical Turk Getting Started Guide](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMechanicalTurkGettingStartedGuide/SetUp.html#aws-security-credentials), and are non-functional.*

These credentials are **not** the username and password used to login at <https://requester.mturk.com>. However, your will have likely already generated appropriate security credentials for use with the tools and frameworks currently being used to create your HITs.

The management interface allows you to select either the [sandbox](https://requestersandbox.mturk.com), or the [production](https://requester.mturk.com) (live) Mechanical Turk service. You may return to the login view by clicking on the *Change* link in the header at any time.

If your credentials are not valid, you will be returned to the login page to try again. In addition to incorrect credentials, login may fail for a variety of reasons including not having an *`AmazonMechanicalTurk`* access policy attached to the credentials or, when attempting to access the sandbox, not being registered to do so. Please check the browser console to determine why your login may have failed.

While this tool does *not* store your credentials, it is advisable to create an additional user and access keys to more easily manage access to your Amazon Mechanical Turk account, and other AWS services.

### Identity and Access Management (IAM)
Rather than using your AWS root security credentials, you can create additional users and access keys using the AWS security console. In the event that such credentials are compromised, they can be revoked individually. The use of IAM credentials is recommended.

Instructions for creating and managing IAM users are available from the [Amazon Mechanical Turk Getting Started Guide](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMechanicalTurkGettingStartedGuide/SetUp.html#create-iam-user-or-role).

If you do not intend to use the interface to modify HITs or assignments in any way (e.g. extend, expire, approve, reject) consider attaching the `AmazonMechanicalTurkReadOnly` access policy to the credentials, rather than `AmazonMechanicalTurkFullAccess`.

* Note: your IAM user must be  using the 2017 version of the `AmazonMechanicalTurkReadOnly` policy (or newer) to allow the `ListHits` permission.

## Manage HITs
Once you have logged in, the console lists all HITs currently available for the requester.

Loading activity is indicated using an overlay and icon, in the centre of the screen.

### Finding & Viewing Tasks
Tasks can be filtered using the status links or the search box. The *Active* status link shows tasks which require further human interaction, the *Waiting* status link shows just tasks which are waiting for approval or rejection, and the *Ended* status link shows tasks for which no further interaction is required and may be deleted. These filters update the presented list of tasks, and summary data detailed below, immediately.

* Search text is case-insensitive and space-separated. The search will match the full `HitId` or any other terms visible in each row.
* To match multi-word phrases, use double quotes (`"`). e.g. the search `my task` will match `my second task` but `"my task"` will **not**.
* Search terms can be negated by prefixing them with a single dash (`-`). e.g. the search `-"a task"` will hide all rows containing the phrase `a task`.
* The special keywords `status/active`, `status/waiting`, and `status/ended` can be used to filter by task status.

You may refresh the active view at any time by clicking the in-page refresh icon.

The *ID* column shows the truncated `HitId`. Selecting this link displays the task specific and Assignment management options, detailed in the *Manage Assignments* section. Hovering over this link displays the full `HitId`, `HitTypeId`, and `HitGroupId` for this task. Despite the truncated display, either a partial or complete `*Id` can be used as the search parameter.

For externally hosted tasks, using an [`ExternalQuestion`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_ExternalQuestionArticle.html), or MTurk hosted [`HTMLQuestion`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_HTMLQuestionArticle.html) tasks a *(Preview)* link is displayed next to the task title, allowing the task to be easily accessed and viewed. The task is linked as it would be from Amazon Mechanical Turk (including additional URL parameters such as `assignmentId=ASSIGNMENT_ID_NOT_AVAILABLE`).

For tasks which have assignments available an *(MTurk)* link is shown, allowing the task to be easily found and viewed on Amazon Mechanical Turk. Where only pending assignments remain, the link is displayed in grey to indicate that assignments may not be available.

### Multi-HIT Actions
At the overview level, the interface offers the following actions which affect all visible HITs. These actions respect the *Active* filter or currently set search value.

* *Download Data*: download a CSV file of answer data grouped by and including `QuestionIdentifier` information and Approval/Rejection information for all visible HITs. Note that timestamps are exported in ISO format.
  * *Amazon*: download a CSV file of answer data in the original Amazon format, for compatibility. Note that all timestamps are PST, see *Known Issues* for more details.
* *Delete*: delete all visible HITs.
  * Note: only HITs which are `Unassignable`, expired or having no available tasks, and for which all assignments have been reviewed may be deleted. Any visible HITs which are ineligible will prevent this action.
  * Note: a download of all data is forced before final confirmation and actual removal of the HITs.

### HIT Status
For ease of recognition, HITs are displayed with various background colours depending on HIT status.

* `Assignable` HITs have outstanding available assignments and are shown with a white background.
* `Unassignable` HITs are complete HITs that require no further user interactions and are shown with a mid-grey background.
* `Unassignable` HITs with unreviewed, submitted assignments are shown with a light-grey background.
  * This is approximately equivalent to tasks in the `Reviewable` or `Reviewing` state.

### Task Assignments
Tasks have a number of assignments, Amazon describes the possible status of these assignments as follows:

* `Pending` assignments for this HIT that are being previewed or have been accepted by Workers
* `Available` assignments for this HIT that are available for Workers to accept
* `Completed` assignments for this HIT that have been approved or rejected

The tool also shows the number of `Submitted` assignments. These are assignments which have been submitted, but not yet reviewed. When present, these counts are shown parenthesised next to the *Pending* figures.

### Summary Data
Summative summary data is shown above and to the right of the table, for all currently displayed tasks.

Summary data indicates the following data, indicated in the following format `T: 0 / P: 0 (+0) / A: 0 (+0) / C: 0`

* `T` number of HITs displayed
* `P` pending assignments, being previewed or currently accepted by workers
  * `(+0)` assignments which have been submitted but are yet to be reviewed
* `A` available assignments
  * `(+0)` assignments which would be available had the HIT not already expired, or that will become available if the HIT is extended
* `C` completed assignments

*Hovering the mouse pointer over the relevant letter or parenthesised number in the summary will show tool-tips in most modern browsers.*

Summary data is updated in real-time, respecting the currently selected filter (*All* or *Active*) or entered search parameters.

## Manage Assignments
The *Manage Assignments* interface includes both HIT specific management actions and assignment management actions.

You can return the HIT overview list at any time by clicking the *Manage HITs* header.

### Task Level Actions
At the task level, the interface offers the following actions.

* *Download Data*: download a CSV file of answer data grouped by and including `QuestionIdentifier` information and Approval/Rejection information. Note that timestamps are exported in ISO format.
  * *Amazon*: download a CSV file of answer data in the original Amazon format, for compatibility. Note that all timestamps are PST, see *Known Issues* for more details.
* *Add Time*: extend an active HIT, or reactivate an expired one. The extension period is specified as an integer number in either *minutes*, *hours* (default), *days*, or *weeks* depending on the drop down selection.
  * Note: reactivated task expiration is subject to a small error, see *Known Issues* for more information.
* *Add Assignments*: add additional assignments to the HIT.
  * Note: not all HITs may have additional assignments created, see *Known Issues* for more information.
* *Expire*: expire an `Assignable` HIT.
* *Delete*: delete the HIT.
  * Note: only HITs which are `Unassignable`, expired or having no available tasks, and for which all assignments have been reviewed may be deleted.

### Assignment Level Actions
Assignments can be filtered by status, `Submitted`, `Approved`, `Rejected`, or *All*. By default, only the submitted assignments are shown as these require requester attention.

You can refresh the list of assignments at any time by clicking the in-page refresh icon.

The *ID* column shows the truncated `AssignmentId`. Hovering over this link displays the full `AssignmentId`.

Assignments are displayed with various background colours depending on their status.

* `Submitted` assignments are displayed with a white background.
* `Approved` assignments are displayed with a light-grey background.
* `Rejected` assignments are displayed with a light-pink background.

The *Time* column shows the times of relevant action for each assignment. Note that where *Approval* is indicated in italics, this is the expected automatic approval time should the requester not explicitly reject the assignment.

At the assignment level, the interface offers the following actions.

* *Reject*: reject an assignment.
  * Note: the interface **requires** the requester to provide feedback to the worker, and rejections must be carried out individually. This limitation is intentional and designed to encourage judicious use of the rejection process.
* *Approve Selected*: approve the *visible* selected assignments.
  * Assignments are selected by checking the checkbox to the leftmost column.
  * All *visible* assignments may be selected at once by using the checkbox in the column header.
  * Previously rejected assignments may be approved from the *Rejected* or *All* views.
* *Bonus*: Send a bonus payment to an individual worker.
  * Bonus payments can only be sent to workers who have carried out tasks in the last six months and in relation to a specific assignment.
  * Bonus payments require a bonus value, in US dollars, and a message explaining the reason for the payment.
    * Note that bonus payments are subject to an [Amazon fee](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_SendBonusOperation.html), similar to the fee charged on the assignment itself.
  * A payment reference is available which must be unique for each payment in a 24 hour period. This defaults to the last six characters of the `WorkerId` and the last six characters of the `AssignmentId`, separated by a hyphen (`-`). In the event that a bonus payment fails, it can be resent with the same reference without the risk of making the payment twice. Should you wish to send a second bonus to the same worker, for the same assignment, in a 24 hour period the reference should be changed.
* *Message*: see the *Other Actions* section below.
* *Message Displayed*: Send a message to all currently displayed workers.
  * This action respects the current filter option. For example, to message only *Approved* workers, select the *Approved* filter before choosing this action.
  * *All*: Send a message to all workers for this HIT.
    * This action is equivalent to selecting the *All* filter and choosing the *Message Displayed* action.


## Other Actions
In addition to actions which strictly relate to existing tasks, the interface also offers *Other Actions*. These are activated using the *Other Actions* button on the *Manage HITs* overview page.

### Message Worker
This action allows the requester to send a message to an individual worker for whom they have previously accepted or rejected an assignment.

Messages can be sent to multiple workers at the same time, the `WorkerId` should be space or comma (`,`) separated, subject to a limit of 100 workers (See *Limitations* for more details).

Messages require a subject (of up to 200 characters) and message content (up to 4,096 characters).

**Note:** The MTurk API does not indicate failure to send a message, and messages to a non-existent or an uncontactable `WorkerId` may appear reported as *Sent*.

### One-off Payment
This action allows you to create a one-off payment task for a given `WorkerId`. *One-off Payment* tasks allow you to quickly create a *make-up* or compensation task for a worker where they have completed a previous task for you but have been unable to submit due to a technical error.

The action creates a Mechanical Turk hosted task unique to the worker. When accepted the task verifies the `WorkerId`, attempts to automatically submit, and pays the listed reward value automatically after the specified delay. Note that these payments are subject to an [Amazon fee](https://requester.mturk.com/pricing), as with any other task.

To prevent unauthorised workers from claiming the reward, unauthorised workers are identified and not permitted to submit the task. Further, the interface creates a unique qualification for the worker, and requires this qualification to find, preview, and accept the task.

While every effort has been made to ensure unauthorised workers will be unable to access and submit the task, **no liability is accepted for misdirected payments**.

Once you have created a *One-off Payment* task, the URL is provided and should be sent to the worker, explaining that they must accept and submit the task to receive their payment. Typically this is done by replying to an email from the worker and their initial enquiry.

**Note**: The *Message Worker* functionality is typically unsuitable for this reply as the requester is unlikely to have accepted or rejected work. The MTurk API does not notify the requester of failure to send a message, and may incorrectly indicate that the message was *Sent*. In instances where the requester has rejected work, an approval is usually the requested remedy of the worker and should be preferred.

Once created, you may view the task in the management interface, as with any other task. The assignment is submitted with a number of parameters for auditing purposes as follows:

* *parameter-assignmentId*
  * `AssignmentId` as specified by MTurk.
* *parameter-hitId*
  * `HitId` as specified by MTurk.
* *parameter-turkSubmitTo*
  * Submission URL as specified by MTurk.
* *parameter-workerId*
  * `WorkerId` as specified by MTurk.
* *javascript-clientTime*
  * The reported client time of the worker's browser, in ISO format (UTC).
* *javascript-userAgent*
  * The reported user-agent string of the worker's browser.
* *javascript-windowLocation*
  * The URL of the task, as hosted by MTurk.
* *javascript-documentReferrer*
  * The URL which brought the worker to the task.
* *javascript-clientIp*
  * The IP address of the worker.
    * This is gathered by the client, at accept time, using the third-party <https://www.ipify.org/> API.

These items are displayed in the interface as the *Answer* for the task and are included in any downloaded data files.

#### Technical Implementation
For reference, the following described the process used to create *One-off Payment* tasks.

1. A custom *"Worker ID is `WorkerId`"* `QualificationTypeId` is created.
2. The `QualificationTypeId` is associated with the `WorkerId`.
3. A `HIT` is created, as follows:
  * Type: `HTMLQuestion`
    *  Hosted by MTurk.
    *  Validates `WorkerId` is as specified.
    *  Provides *Submit* button and automatically attempts to submit.
    *  Automatically approves once submitted, after the specified delay.
  * Requires the `QualificationTypeId` created.
    * `QualificationTypeId`: `Exists`
    * `ActionsGuarded `: `DiscoverPreviewAndAccept`


### Manage Qualifications
This action provides an interface for creating, managing, and removing qualifications.

**Note**: All actions in this section of the interface are effected immediately.

#### Viewing Qualifications
On opening the interface, all qualification types owned by the requester are listed. This list can be filtered using the search box as for *[Finding & Viewing Tasks](#finding--viewing-tasks)*.

The truncated `QualificationTypeId` is shown in the leftmost column, and hovering displays the full `QualificationTypeId`.

A list of assigned workers can be seen by clicking the truncated `QualificationTypeId`. See *[Viewing Qualified Workers](#viewing-qualified-workers)* for further details.

A complete list of qualifications owned by the requester can be downloaded using the *Download All* button. **Note**: this download ignores any currently specified search filter.

The list of qualification types can be refreshed using the in-dialogue refresh icon.

#### Creating Qualifications
New qualification types can be created by choosing the *Create Qualification* button next to the search box in the main *Manage Qualifications* interface.

The *Create* dialogue box prompts for a *Name*, *Description*, and list of comma separated *Keywords*. The name of the qualification must be unique among qualifications owned by the requester. If the *Description* is not populated, the title text is repeated in the description. *Keywords* are optional.

All qualifications created with this interface add an additional keyword `mturk-manage-qualification/*`. This is for internal management purposes.

**Note**: this action may take a short period to take effect. The `QualificationTypeID` is returned immediately and is entered in the search box. As such, the interface may indicate no matching qualification types. The listing can be refreshed using the in-dialogue refresh icon.

#### Removing Qualifications
Qualification types can be removed by clicking the *Remove* button in the right most column of the main *Manage Qualifications* interface.

**Note**: this action may take a short period to take effect.

#### Viewing Qualified Workers
On clicking the truncated `QualificationTypeId` in the main *Manage Qualifications* interface the list of assigned workers is displayed. This list can be filtered using the search box as for *[Finding & Viewing Tasks](#finding--viewing-tasks)*.

In addition, workers can be filtered by the granted value by prefixing the desired value with the special keyword `value/`. For example, `value/5` would filter for workers with a currently granted value beginning with `5`, i.e. this would also match a value of (for example) `57`. To limit to the precise value a second forward slash (`/`) can be added, e.g. `value/5/`. This would only match workers with a currently specified value of `5`.

The list of workers assigned this qualification can be refreshed using the in-dialogue refresh icon.

The *Copy Shown* option above the *ID* column allows the currently displayed list of workers to be copied to the clipboard.

A complete list of qualified workers can be downloaded using the *Download All* button. **Note**: this download ignores any currently specified search filter.

#### Assigning Qualifications
Workers may be assigned the qualification by choosing the *Add Workers* button. Each `WorkerId` should be separated by a space, comma, or new line.

The qualification may be assigned with an associated integer value using the *Value* box. The default value is *1*. In the event that this value needs to be updated, can be effected by reassigning the qualification to the worker with the correct value. **Warning**: Non-integer values are automatically assigned with the value *0*.

**Note**: Each worker is assigned the qualification individually. While any number of workers may be added, large numbers of workers may take several seconds to process. Please wait for the activity indicator to complete.

In the event of an error, such as a `WorkerId` which could not be processed, the action terminates. Due to the nature of assigning the qualifications, this action is not atomic and any `WorkerId` up to that point will have been assigned as requested. To aid in resuming the operation a list of unprocessed IDs, **excluding** the offending `WorkerId`, is provided. This list of unprocessed IDs may be copied using the *Copy All* link, ready to be pasted into a subsequent *Add Workers* request.

#### Revoking Qualifications
In the event that a worker has been added in error, or you wish to revoke a qualification, qualifications can be unassigned using the appropriate *Remove* button in the right-most column of the worker listing.


### Visit mturk.com
This action provides a quick link to the appropriate requester page at mturk.com to manage additional worker actions including blocking workers and viewing currently assigned qualifications.



## Known Issues
* When extending an expired task, the expected expiry time indicated may be slightly inaccurate.
  * This appears to be an issue with the API. While the interface specifies the precise expiration time in the API call, Amazon appears not to respect this for currently expired HITs. Extensions to currently active HITs are typically precise and as indicated.
* Tasks created with fewer then 10 assignments may not be extended to have 10 or more assignments.
  * This is a limitation of Amazon Mechanical Turk. For more information see the [Amazon Mechanical Turk API Reference](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_CreateAdditionalAssignmentsForHITOperation.html).
* Amazon formatted CSV export using the same timestamp format as the original Amazon Mechanical Turk exports, however all timestamps are exported as PST. Previously, Amazon would report the timestamp in PST (-8 hours) or PDT (-7 hours) as was appropriate to Seattle, Washington. The exported timestamps are calculated using a negative 8 hour offset from UTC, and are always reported in PST regardless of the current DST offset of Amazon HQ.
  * ISO format example: *2018-01-23T08:27:02.000Z*
  * *Amazon* format example: *Tue Jan 23 12:27:02 PST 2018*

## Limitations
This tool has been developed rapidly, and primarily with the needs of the author in mind.

The interface has only undergone limited testing, primarily with [`ExternalQuestion`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_ExternalQuestionArticle.html) type HITs and with the [Locale Qualification](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_QualificationRequirementDataStructureArticle.html#ApiReference_QualificationRequirementDataStructureArticle-the-locale-qualification).

* HITs which do not include an [`ExternalURL`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_ExternalQuestionArticle.html#ApiReference_ExternalQuestionArticle-the-externalquestion-data-structure) or of the type [`HTMLQuestion`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_HTMLQuestionArticle.html) will not have a *(Preview)* link.
* Only three built in qualifications are identified: *Locale*, to country level; *Masters*; and *Adult*. Other qualifications are indicated as a count only.

By default, dates and times are shown in the interface in the browser reported timezone using [`toLocaleDateString()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleDateString). You may also select GMT (UTC), ISO (UTC) or Amazon formats. When exporting data timestamps are reported using ISO format (UTC) or Amazon equivalent format (PST, as [detailed above](#known-issues)).

Where existing functionality was retained in the official interface, it may not be replicated here. For example, blocking workers may still be done from the official requester [workers page](https://requester.mturk.com/workers).

To minimise the number of API calls, the interface caches the HIT and Assignment data for 5 minutes. If you wish to manually refresh the results, you may click the in-page refresh icon to the right of the search field and assignment filters.

The tool loads all relevant data on each refresh, and does not paginate the results. When managing several hundred tasks or assignments, there may be a small delay in loading the data from the API. Loading activity is indicated using a loading overlay and icon. The tool has been tested, and is functional, with in excess of 500 active tasks loaded.

A maximum of 100 workers may be messaged at once.

* This is the result of a limitation of Amazon Mechanical Turk. For more information see the [Amazon Mechanical Turk API Reference](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_NotifyWorkersOperation.html).
* Should you have a HIT that has more than 100 workers whom you wish to message, this can be accomplished by downloading the data and pasting the `WorkerId`s into the *Worker IDs* box in groups of up to 100. Each `WorkerId` should be separated using a space or comma (`,`) character.
  * Pasting into the text box directly from *Excel* or another spreadsheet application should be sufficient.

The parameters specified in *(Preview)* links include the additional parameter `source=manage-hits-console` to aid in identification of previews generated by this tool.

When set to pay *Immediately*, the *One-off Payment* tasks may take a few minutes to actually approve. This is a limitation of MTurk, which appears to use a periodic rather than event driven approach to making these payments.


# Development

## Change Log

### 2020-09-02
* Added `value/` keyword filtering for qualifications.
* Switched to a fully modal loading icon, to avoid issues with modal windows from jQuery UI.
* Added explicit login error message.
* Minor documentation updates.
* Updated AWS SDK to 2.744.0 (was 2.742.0)

### 2020-08-30
* Renamed *Worker Actions* to *Other Actions*
* Added *Manage Qualifications* option to *Other Actions*
* Search is now preserved when returning to *Manage* mode.
* Updated copyright dates.
* Updated jQuery to 3.5.1 (was 3.4.1)
* Updated AlaSQL to 0.6.3 (was 0.5.1)
* Updated AWS SDK to 2.742.0 (was 2.585.0)

### 2019-12-06
* Fixed typo in one-off payment task.
* Added `onbeforeunload` exit check.
* Updated URL and license for Reload icon.
* Updated copyright dates.
* Updated jQuery to 3.4.1 (was 3.3.1)
* Updated AlaSQL to 0.5.1 (was 0.4.5)
* Updated AWS SDK to 2.585.0 (was 2.224.1)

### 2018-04-16
* Removed default action for *One-off Payments* and explicitly require JavaScript.
* Added active HIT check to *Multi-HIT Delete* button ([#1](https://github.com/jtjacques/mturk-manage/issues/1)).
* Bumped AWS SDK to 2.224.1 (was 2.223.1)

### 2018-04-10
* Added *Worker Actions* functionality.
  * *Message Worker*: (added button) send a message any previously approved or rejected `WorkerId`.
  * *One-off Payment*: create a *make-up* or compensation task for any `WorkerId`.
  * *Visit mturk.com*: link to <https://requester.mturk.com> for managing worker block status and qualifications.
* Added timezone display option to login screen.
  * Default to local timezone, as reported by the browser.
  * Note: only affects displayed times, downloaded data is still in ISO or Amazon format.
* Added check to date processing, to catch `undefined` dates.
* Added expected automatic approval time in italics.
* Added basic *Rate Limit* check to high-frequency actions.
* Added *Preview* for HITs with a `HTMLQuestion`.
* Automatically reset view on mode change.
* Bumped AWS SDK to 2.223.1 (was 2.212.1)

### 2018-03-22
* Fixed overview level actions regression caused by introducing tool tips.
* Moved activity indicator to float.
* Fixed minor graphical glitch.

### 2018-03-21
* Added `HitId`, `HitTypeId`, `HitGroupId`, and `AssignmentId` hover-over tool tips.
* Minor documentation changes.
* Bumped AWS SDK to 2.212.1 (was 2.211.1)

### 2018-03-20
* Switched to MIT License.
  * Removed cc-by-sa 3.0 licensed CSV export code.
  * Added external dependency on AlaSQL (0.4.5).
* Improved search functionality.
  * Search now matches all terms, but in any order, and allows quoting.
* Added manual refresh functionality.
  * Lengthened default refresh to 5 minutes.
* Added SRI hashes to all third-party external resources.
* Bumped AWS SDK to 2.211.1 (was 2.210.0)

### 2018-03-17
* Fixed bug in overview *Download* functionality where headers may be misaligned in cases where column names differ between visible tasks.

### 2018-03-16
* Added overview level actions: *Download*, *Amazon*, and *Delete*.
* Added account balance to header.
* Added usage note advising against web-deployment.
* Corrected documentation for *Add Time* functionality.
* Expanded documentation for *Bonus* and *Message* functionality.
* Added note about older versions of the `AmazonMechanicalTurkReadOnly` policy not allowing the `ListHits` operation.
* Fixed minor errors in documentation.
* Bumped AWS SDK to 2.210.0 (was 2.188.0)

### 2018-02-02
* Avoid calling the API where empty results sets will occur.
  * Only request additional HIT and Assignment results if the API returns the maximum allowed in the previous call.
* Replaced all `alert` and `prompt` pop-ups with jQuery UI dialogs to allow for richer user interactions.
  * Added external dependency on jQuery UI (1.12.1)
* Added `iframe` previews for `ExternalQuestion` HITs
  * Added simulated acceptance of tasks.
* Added worker management features
  * *Bonus*: Pay workers bonus
  * *Message*: Send messages to workers
* Bumped AWS SDK to 2.188.0 (was 2.186.0)

### 2018-01-23
* Initial release

## TODOs
* Investigate alternative login solutions.
  * See: [AWS SDK for JavaScript](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-credentials-browser.html)
* Possible pagination of tasks and assignments.
* ~~Improve search functionality.~~
* Extend and improve qualifications information.
* Possible task creation support.
  * ~~Create one-off payment tasks.~~
* ~~Add worker contact and additional management options.~~
  * Possibly add *Block* functionality.
* ~~Provide `iframe` previews of tasks.~~
* Allow direct access to a given `HITId`.

## Acknowledgements
This tool is based on, includes, links to, or extends a number of additional resources. These are acknowledged here.

* Amazon AWS SDK
    * Amazon.com, Inc.
    * <https://github.com/aws/aws-sdk-js>
    * License: [Apache License 2.0](https://github.com/aws/aws-sdk-js/blob/master/LICENSE.txt)
* jQuery
    * JS Foundation
    * <https://jquery.com>
    * License: [MIT License](https://jquery.org/license/)
* jQuery UI
    * JS Foundation
    * <https://jqueryui.com>
    * License: [MIT License](https://jquery.org/license/)
* AlaSQL
  * Andrey Gershun
    * <https://github.com/agershun/alasql>
    * License: [MIT License](https://github.com/agershun/alasql/blob/develop/LICENSE)

* Ajax-loader.gif (optimised, base64 encoded, embedded)
  * ajaxload.info (<http://www.ajaxload.info>)
  * <https://commons.wikimedia.org/wiki/File:Ajax-loader.gif>
  * License: [Public Domain](https://commons.wikimedia.org/wiki/File:Ajax-loader.gif#Licensing)
* Reload (embedded)
  * David Merfield
  * <https://github.com/davidmerfield/Public-Icons/blob/master/source/icons/reload.svg>
  * License [CC0 Public Domain Dedication](https://github.com/davidmerfield/Public-Icons/blob/master/LICENSE)

* ipify: <https://www.ipify.org/>

* Enable JavaScript: <https://enable-javascript.com>

## Contact
Copyright &copy; 2018-2020 Jason T. Jacques

* Email: [jtjacques@gmail.com](mailto:jtjacques@gmail.com?subject=mturk-manage.html)
* Web: [jsonj.co.uk](https://jsonj.co.uk)

## Licenses

mturk-manage.html is released under the terms of the [MIT License](https://github.com/jtjacques/mturk-manage/blob/master/LICENSE).

Ajax-loader.gif is [public domain](https://commons.wikimedia.org/wiki/File:Ajax-loader.gif#Licensing), as acknowledged above, and is included under such provision.

Reload icon is released under the [CC0 Public Domain Dedication](https://github.com/davidmerfield/Public-Icons/blob/master/LICENSE), as acknowledged above, and is included under such provision.

Externally loaded resources ([AWS SDK](https://github.com/aws/aws-sdk-js/blob/master/LICENSE.txt), [jQuery](https://jquery.org/license/), [jQuery UI](https://jquery.org/license/), [AlaSQL](https://github.com/agershun/alasql/blob/develop/LICENSE)) are subject to their own licenses.
