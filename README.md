# MTurk Manage
mturk-manage.html reproduces some of the basic functionality offered by the *Mange HITs individually* option [removed from the Amazon Mechanical Turk requester interface in December 2017](https://blog.mturk.com/upcoming-changes-to-the-mturk-requester-website-and-questionform-data-format-f7c3238be58c).

**This tool is offered without warranty.**

The tool broadly replicates the functionality and layout of the discontinued interface, and is designed to be familiar to those who have previous experience managing HITs.

The interface runs locally in the browser, and has only two external dependencies, [jQuery](https://code.jquery.com) and the [Amazon AWS SDK](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/loading-the-jssdk.html), which are loaded automatically.

**While the management console does not store credentials, or send them to any third party, you accept all liability for the security of your access credentials when using them with this tool.**

Guidance on limiting the access of your credentials is offered in the *Login* section.

# Usage

## Login
The login page allows you to enter your API key and API secret as used to create and manage tasks. You will need to enter your AWS *access key ID* and *secret access key*. e.g.

* Sample access key ID: AKIAIOSFODNN7EXAMPLE
* Sample secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

*These credentials are examples only, reproduced from the [Amazon Mechanical Turk Getting Started Guide](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMechanicalTurkGettingStartedGuide/SetUp.html#aws-security-credentials) and are non-functional.*

These credentials are **not** the username and password used to login at <https://requester.mturk.com>. However, your will have likely already generated appropriate security credentials for use with the tools and frameworks currently being used to create your HITs.

The management interface allows you to select either the [sandbox](https://requestersandbox.mturk.com), or the [production](https://requester.mturk.com) (live) Mechanical Turk service. You may return to the login view by clicking on the *(change)* link in the header at any time.

If your credentials are not valid, you will be returned to the login page to try again. In addition to incorrect credentials, login may fail for a variety of reasons including not having an *`AmazonMechanicalTurk`* access policy attached to the credentials or, when attempting to access the sandbox, not being registered to do so. Please check the browser console to determine why your login may have failed.

While this tool does *not* store your credentials, it is advisable to create an additional user and access keys to more easily manage access to your Amazon Mechanical Turk account, and other AWS services.

### Identity and Access Management (IAM)
Rather than using your AWS root security credentials, you can create additional users and access keys using the AWS security console. In the event that such credentials are compromised, they can be revoked individually. The use of IAM credentials is recommended.

Instructions for creating and managing IAM users are available from the [Amazon Mechanical Turk Getting Started Guide](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMechanicalTurkGettingStartedGuide/SetUp.html#create-iam-user-or-role).

If you do not intend to use the interface to modify HITs or assignments in any way (e.g. extend, expire, approve, reject) consider attaching the `AmazonMechanicalTurkReadOnly` access policy to the credentials, rather than `AmazonMechanicalTurkFullAccess`.

## Manage HITs
Once you have logged in, the console lists all HITs currently available for the requester.

Loading activity is indicated using an icon in the top right of the management console, under the *(change)* login link.

### Actions
Tasks can be filtered both by the *Active* status, where the task requires further human interaction, or using the search box. These filters update the presented list of tasks, and summary data detailed below, immediately.

* Note: search text is used in its entirety, e.g. *"my task"* will **not** match *"my second task"*.

The *ID* column shows the truncated `HitId`. Selecting this link displays the task specific and Assignment management options, detailed in the *Manage Assignments* section. Despite the truncated display, either a partial or complete `HitId` can be used as the search parameter.

For externally hosted tasks, using an [`ExternalQuestion`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_ExternalQuestionArticle.html), the a *(url)* link is displayed next to the task title, allowing the task to be easily accessed and viewed. The task is linked as it would be from Amazon Mechanical Turk (including additional URL parameters such as `assignmentId=ASSIGNMENT_ID_NOT_AVAILABLE`), however it is not presented in an `iframe`.

For tasks which have assignments available an *(mturk)* link is shown, allowing the task to be easily found and viewed on Amazon Mechanical Turk.

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
	* *(Amazon)*: download a CSV file of answer data in the original Amazon format, for compatibility. Note that all timestamps are PST, see *Known Issues* for more details.
* *Add Time*: extend an active HIT, or reactivate an expired one. The extension period is specified in whole number of hours. Alternatively, the duration may be specified in days, using the special suffix `d` (e.g. `14d` can be entered to indicate two weeks instead of `336`).
	* Note: reactivated task expiration is subject to a small error, see *Known Issues* for more information. 
* *Add Assignments*: add additional assignments to the HIT.
	* Note: not all HITs may have additonal assignments created, see *Known Issues* for more information.
* *Expire*: expire an `Assignable` HIT.
* *Delete*: delete the HIT.
	* Note: only HITs which are `Unassignable`, expired or having no available tasks, and for which all assignments have been reviewed may be deleted.

### Assignment Level Actions
Assignments can be filtered by status, `Submitted`, `Approved`, `Rejected`, or *All*. By default, only the submitted assignments are shown as these require requester attention.

Assignments are displayed with various background colours depending on their status.

* `Submitted` assignments are displayed with a white background
* `Approved` assignments are displayed with a light-gray background
* `Rejected` assignments are displayed with a light-pink background

At the assignment level, the interface offers the following actions.

* *Reject*: reject an assignment.
	* Note: the interface **requires** the requester to provide feedback to the worker, and rejections must be carried out individually. This limitation is intentional and designed to encourage judicious use of the rejection process.
* *Approve Selected*: approve the *visible* selected assignments.
	* Assignments are selected by checking the checkbox to the leftmost column.
	* All *visible* assignments may be selected at once by using the checkbox in the column header.
	* Previously rejected assignments may be approved from the *Rejected* or *All* views.

## Known Issues
* When extending an expired task, the expected expiry time indicated may be slightly inaccurate.
	* This appears to be an issue with the API. While the interface specifies the precise expiration time in the API call, Amazon appears not to respect this for currently expired HITs. Extensions to currently active HITs are typically precise and as indicated.
* Tasks created with fewer then 10 assignments may not be extended to have 10 or more assignments.
	* This is a limitation of Amazon Mechanical Turk. For more information see the [Amazon Mechanical Turk API Reference](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_CreateAdditionalAssignmentsForHITOperation.html).
* Amazon formatted CSV export using the same timestamp format as the original Amazon Mechanical Turk exports, however all timestamps are exported as PST. Previously, Amazon would report the timestamp in PST (-8 hours) or PDT (-7 hours) as was appropriate to Seattle, Washington. The exported timestamps are calculated using a negative 8 hour offset from UTC, and are always reported in PST regardless of the current DST offset of Amazon HQ.
	* ISO format example: *2018-01-23T08:27:02.000Z*
	* From mturk.com: *Tue Jan 23 12:27:02 PST 2018*

## Limitations
This tool has been developed rapidly, and primarily with the needs of the author in mind.

The interface has only undergone limited testing, primarily with [`ExternalQuestion`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_ExternalQuestionArticle.html) type HITs and with the [Locale Qualification](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_QualificationRequirementDataStructureArticle.html#ApiReference_QualificationRequirementDataStructureArticle-the-locale-qualification).

* HITs which do not include an [`ExternalURL`](https://docs.aws.amazon.com/AWSMechTurk/latest/AWSMturkAPI/ApiReference_ExternalQuestionArticle.html#ApiReference_ExternalQuestionArticle-the-externalquestion-data-structure) will not have a *(url)* link and cannot be previewed when their state is `Unassignable`.
* Only three built in qualifications are indicated: *Locale*, to country level; *Masters*; and *Adult*.

Dates and times are shown in the interface in UTC using the [`toUTCString()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toUTCString) method. When exporting data timestamps are reported using ISO or Amazon equivalent formats.

Where existing functionality was retained in the official interface, it is not replicated here. For example, bonus payments may still be made from the official requester [workers page](https://requester.mturk.com/workers).

To minimise the number of API calls, the interface caches the HIT and Assignment data for 60 seconds. If you do not wish to change the mode of operation, clicking on the appropriate *All* filter will refresh the cache after this time. The caching period is configured, in the code, using the `__session['refresh-freq']` field and is indicated in milliseconds.

The tool loads all relevant data on each refresh, and does not paginate the results. When managing several hundred tasks or assignments, there may be a small delay in loading the data from the API. Loading activity is indicated in the top right of the console. The tool has been tested, and is functional, with in excess of 350 active tasks loaded.

While HIT *(url)* links do include additional URL parameters such as `assignmentId=ASSIGNMENT_ID_NOT_AVAILABLE` to detect task status, they are not presented in an `iframe`. This may impact functionality of some previews where the HIT presentation is dependant on whether or not the task is being presented inside a frame.

* The *(url)* links additionally include the parameter `source=manage-hits-console` to aid in identification of these previews.

# Development

## TODOs
* Investigate alternative login solutions e.g. <https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-credentials-browser.html>
* Possible pagination of tasks and assignments.
* Improve search functionality.
* Extend and improve qualifications information.
* Add worker contact and additional management options.
* Provide `iframe` previews of tasks.
* Allow direct access to a given `HitId`.

## Acknowledgements
This tool is based on, includes, or extends a number of additional resources, acknowledged here.

* jQuery
  * <https://jquery.com>
  * License: [MIT license](https://jquery.org/license/)
* Amazon AWS SDK
  * <https://github.com/aws/aws-sdk-js>
  * License: [Apache License 2.0](https://github.com/aws/aws-sdk-js/blob/master/LICENSE.txt)

* CSV file download support, Xavier John
  * <https://stackoverflow.com/a/24922761>
  * License: [cc by-sa 3.0](https://creativecommons.org/licenses/by-sa/3.0/)
* Ajax-loader.gif, optimised, base64 encoded
	* <https://commons.wikimedia.org/wiki/File:Ajax-loader.gif>
	* License: [Public Domain](https://en.wikipedia.org/wiki/en:public_domain)

* Enable JavaScript: <https://enable-javascript.com>


## Contact
Copyright &copy; 2018, Jason T. Jacques

* Email: [jtjacques@gmail.com](mailto:jtjacques@gmail.com?subject=mturk-manage.html)
* Web: [jsonj.co.uk](https://jsonj.co.uk)

## Licenses

mturk-manage.html is licensed under [cc by-sa 3.0](https://creativecommons.org/licenses/by-sa/3.0/) to comply with licenses of included components.

CSV file download support is licensed under [cc by-sa 3.0](https://creativecommons.org/licenses/by-sa/3.0/), as acknowledged above, and included under such provision.

Ajax-loader.gif is [public domain](https://en.wikipedia.org/wiki/en:public_domain), as acknowledged above, and is included under such provision.

Externally loaded resources ([jQuery](https://jquery.org/license/), [AWS SDK](https://github.com/aws/aws-sdk-js/blob/master/LICENSE.txt)) are subject to their own licenses.