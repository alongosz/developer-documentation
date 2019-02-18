# Development guidelines

These are the development/coding guidelines for eZ Platform packages. They are the same if you
intend to write Bundles, hack on eZ Platform itself or create new functionality for or on top of
eZ Platform.

Like most development guidelines these aims to improve security, maintainability, performance,
and readability of our software. They follow industry standards but sometimes extend them to cater
specifically to our needs for eZ Platform ecosystem. The next sections will cover all relevant
technologies from a high level point of view.

## Source code

eZ Platform Open Source Edition codebase is available at [GitHub eZ Systems organization](https://github.com/ezsystems).
Most of the source code which provides features is scattered across various packages (GitHub repositories), e.g.:

- [ezpublish-kernel](https://github.com/ezsystems/ezpublish-kernel), which provides Public and REST API
and implements Legacy Storage persistence layer.
- [ezplatform-admin-ui](https://github.com/ezsystems/ezplatform-admin-ui), which provides
administration interface (a.k.a. AdminUI, back office) for eZ Platform.

## Contribution workflow

In general the workflow for making a contributions is as follows:

1. Report a JIRA issue.
2. Clone the proper repository.
3. Create your own branch.
4. Make changes and commit them.
5. Push the branch with your changes and create a pull request.
6. Describe pull request
7. Make sure Travis CI shows that all checks (jobs) have passed.
8. Ask for a code review.
9. Address the requested changes after a code review.
10. If everything is in order wait for our Quality Assurance Team to test the changes.
11. Address the issues found during testing, if any.
12. Once QA approves, ask a package maintainer to merge changes (usually handled by eZ Engineering
    automatically).

## JIRA issue tracker

Whether you've found a bug or want to add new feature, it requires JIRA issue reported on
https://jira.ez.no/browse/EZP or https://jira.ez.no/browse/EZEE for Open Source or Enterprise
Editions respectively.
If you don't have an account there yet, you can [sign up here](https://login.ez.no/register).

There are two types of issues we support right now: "Bug" and "Story". Other types of issues will be
disregarded.

In case of bugs, exact steps to reproduce are required in order for us to understand what is
happening and how it can be tested.

In case of new features there are a few more things to pay attention to:

- Issue Type should be a `Story` in a form of `As a <persona type> I want <to do something>`,
where `persona type` is one of the following:
    - User - related to features intended for end-users of the application.
    - Administrator - related to administrative tasks like creating Content Types, Languages, Roles, etc.
    - Editor - related to editorial tasks and workflow.
    - Developer - related to extensibility points and developer experience improvements.
    - Maintainer - related to dev-ops activities such as installing and setting up eZ Platform.

- New feature cannot break existing features for other users.

## Packages and their versions

@todo describe

## Working with source code on GitHub

Contributing to eZ Platform OSS packages is done by forking specific package to Developer's own
GitHub account and cloning package repository from that fork.

After cloning a repository from GitHub, checkout proper branch and from that branch
create a feature or a patch branch for Stories or Bugs respectively.

Branch name should be formed using small caps, separated by dashes and should start with
JIRA issue number followed by brief, descriptive keywords related to a change, e.g.:
`ezp-25590-dev-contribution-guide`.

Do your changes and commit them to your branch. Commits should be formed in **past tense** stating
what the given commit did, e.g. `Implemented ContentService::publishVersion API` or
`Fixed incorrect number of arguments passed to SomeClass::someMethod`.

For better readability it is recommended to keep initial commit message 72 at most characters long
and put more detailed description in commit description field.
Commits should be atomic. If a feature or a bug requires changes which can be described separately
(e.g. affecting several parts of a system), each such change should be committed separately.

When working either on a feature or a bug fix, **make sure to include tests**. Solution without tests
isn't considered as valid. Tests protect us against future regressions and allow others to fully
understand the issue at hand.

## Making pull requests

After committing changes, to contribute them to the upstream, push your branch to your fork and
[create a pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request).

Pull request title needs to lead with JIRA issue number, followed by colon and issue title.
Title should be formed in past tense (stating what PR changed or fixed with respect to the package
source code), e.g.: `EZP-25590: Added contribution guide for developers`.

If a pull request description contains already pre-defined templates (applies to some repositories),
make sure to edit that template to address all the necessary questions. If a checklist
(or a TODO list) is given, make sure to address that as well.

Make your pull request description as detailed as possible. Pull requests undergo a code review
process, so the better the description, the smoother the process is. Before posting a pull request,
review the changes yourself. Think about reviewers of your code. Think what might not be clear at
first when looking through your suggested changes and try to explain that in the pull request
description.

After making a pull request, Travis CI will kick in, automatically executing some predefined test
jobs. All jobs need to pass, before a pull request can be processed further.

Once everything is in order, ask for a code review. If you don't know who to ask, there are several
possibilities:

- Visit [eZ Community Slack](http://ez-community-on-slack.herokuapp.com). After signing up find a
  proper channel related to a eZ Platform version you've contributed to e.g.: `#ezplatform-v2`,
  `#ezplatform-v3` and ask for a review.
- Find the latest contributors from eZ Systems organization to the package you've created a pull
  request for and ping them directly.
- Ping `@ezsystems/engineering-team` for a review.

The general rule is that the pull request requires at least 2 approvals from the eZ Engineering Team
before it can be processed further. Please note that this process might take significant amount of
time due to our other tasks. If you're our Partner or Customer you can speed it up by reaching out
to our Customer Success Team so a review can be scheduled during our planning (plannings are done
usually every two weeks).

Contributing to eZ Platform Enterprise Edition packages is allowed only to specific people as the
source code is closed (packages are private). If you have access and ability to contribute, there is
just one difference - you need to push your feature or patch branch directly to the `ezsystems`
organization due to the technical limitations of our CI pipeline.

**Keep in mind that if some of those steps are not followed, your pull request will not be
processed**.

## HTTP

eZ Platform is a web software that is reached via HTTP in most cases, out of the box in eZ Platform kernel this is specifically: web (usually HTML) or REST.

We aim to follow the [latest](http://trac.tools.ietf.org/wg/httpbis/trac/wiki#HTTP1.1Deliverables) stable HTTP specification, and industry best practice:

- **Expose our data in a RESTful way**
    - GET, HEAD, OPTIONS and TRACE methods are [safe](http://tools.ietf.org/html/draft-ietf-httpbis-p2-semantics-21#section-5.2.1) (otherwise known as [nullipotent](http://en.wiktionary.org/wiki/nullipotent)), as in: should never cause changes to resources (note: things like writing a line in a log file are not considered resource changes)
    - PUT and DELETE methods are [idempotent](http://tools.ietf.org/html/draft-ietf-httpbis-p2-semantics-21#section-5.2.2), as in multiple identical requests should all have the same result as a single request
    - GET and HEAD methods should be [cacheable](http://tools.ietf.org/html/draft-ietf-httpbis-p2-semantics-21#section-5.2.3) both on client side, server-side and proxies between, as further defined in the HTTP [specification](http://tools.ietf.org/html/draft-ietf-httpbis-p6-cache-21)
    - As PUT is for replacing a resource, we should use [PATCH](http://tools.ietf.org/html/rfc5789) in cases where only partial replacement is intended
- **Authenticated traffic**
    - Should use HTTPS
- **Session based traffic**
    - Should follow recommendations for *Authenticated traffic*
    - Should use a per user session [CSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery) token on all requests using un-safe HTTP methods (POST, PUT, DELETE, PATCH, ...)
    - Should expire session ID, session data and CSRF token on login, logout and session time out, except:
        - On login session data from previous session ID is moved to new session ID, keeping for instance shopping basket on login
    - Should avoid timing attacks by using a random amount of time for login operation
    - Should never use Session ID in URI's. And this feature ("SID") must always be disabled on production servers
- **Sessions**
    - Should not be used to store large amounts of data; store data in database and ID's in session if needed
    - Should not store critical data: if user deletes his cookies or closes his browser session data is lost
    - Should use an ID generated with enough randomness to prevent prediction or brute-force attacks
- **Cookies (especially session cookies)**
    - Should never store sensitive data in cookies (only exception is session ID in session cookie)
    - Should always set *Full domain* to avoid [cross-subdomain cooking](https://en.wikipedia.org/wiki/Session_fixation#Attacks_using_cross-subdomain_cookie) when on shared domain.
    - Should set *HttpOnly* flag to reduce risk of attacks such as [cross-site cooking](https://en.wikipedia.org/wiki/Cross-site_cooking) and [cross-site scripting](http://en.wikipedia.org/wiki/Cross-site_scripting "Cross-site scripting")
    - Should set *Secure flag* if HTTPS is used (as recommended above)
    - Must never exceed 4kB
- **Headers**
    - Should never include input data from user input or data from database without sanitizing it
- **Redirects**
    - Should never take URL from user input (example: POST parameter), instead allow identifiers instead that are understood by the backend
- **User input**
    - Should always be validated, sanitized, casted and filtered to avoid [XSS](http://en.wikipedia.org/wiki/Cross-site_scripting) and [clickjacking](http://en.wikipedia.org/wiki/Clickjacking) attacks
        - Note: this includes variables in the php supervariable `$_SERVER` as well (e.g. hostname should not be trusted)
- **User file uploads**
    - Should follow recommendations for "User input" to validate file name
    - Should place uploaded files in a non public folder to avoid access to execute uploaded file or in case of assets white list the type
    - Should be appropriately limited in size to avoid DOS attacks on disk space, CPU usage by antivirus tool etc...
- **File downloads**
    - Should not rely on user provided file path for non public files, instead use a synthetic ID
- **Admin operations**
    - May be placed on a different (sub)domain then the front end website to avoid session stealing across front and backend.
- **Fully support being placed behind a reverse proxy like [Varnish](https://www.varnish-cache.org/)**

### REST

For now see the living [REST v2 specification](https://github.com/ezsystems/ezp-next/blob/master/doc/specifications/rest/REST-API-V2.rst) in our git repository for further details.

## UI

eZ Platform is often used as a web content management software, so we always strive to use the HTML/CSS/EcmaScript specifications correctly, and keep new releases up to date on new revisions of those. We furthermore always try to make sure our software gracefully degrades making sure it is useful even on older or less capable web clients (browsers), the industry terms for this approach are:

- [Progressive enhancement](http://en.wikipedia.org/wiki/Progressive_enhancement "Progressive enhancement")
- [Unobtrusive JavaScript](http://en.wikipedia.org/wiki/Unobtrusive_JavaScript)
- [Responsive Design](http://en.wikipedia.org/wiki/Responsive_Web_Design "Responsive Web Design")

All these terms in general recommends aiming for the minimum standard first, and enhance with additional features/styling if the client is capable of doing so. In essence this allows eZ Platform to be "Mobile first" if the design allows for it, which is recommended. But eZ Platform should always also be fully capable of having different sets of web presentations for different devices using one or several sets of SiteAccess matching rules for the domain, port or URI, so any kind of device detection can be used together with eZ Platform, making it fully possible to write for instance [WAP](http://en.wikipedia.org/wiki/Wireless_Application_Protocol) based websites and interfaces on top of eZ Platform.

### WEB Forms/Ajax

As stated in the HTTP section, all unsafe requests to the web server should have a CSRF token to protect against attacks; this includes web forms and ajax requests that don't use the GET http method. As also stated in the HTTP section and further defined in the PHP section, User input should always be validated to avoid XSS issues.

### HTML/Templates

All data that comes from backend and in return comes from user input should always be escaped, in case of Twig templates this done by default, but in case of PHP templates, Ajax and other not Twig based output this must be handled manually.


Output escaping must be properly executed according to the desired format, eg. JavaScript vs. HTML, but also taking into account the correct character set (see e.g. output escaping fallacy when not specifying charset encoding in [htmlspecialchars](http://www.php.net/htmlspecialchars))

### Admin

Admin operations that can have a severe impact on the web applications should require providing password and require it again after some time has gone, normally 10-20 minutes, on all session-based interfaces.

## PHP

### Public API

The [Public PHP API](../api/public_php_api) provided in eZ Platform is in most cases in charge of checking permissions to data for you, but some API's are not documented to throw `UnauthorizedException`, which means that it is the consumer of the API's who is responsible for checking permissions.

The following example shows how this is done in the case of loading users:

**loadUser()**

``` php
// Get a user
$userId = (int)$params['id'];
$userService = $repository->getUserService();
$user = $userService->loadUser( $userId );

// Now check that current user has access to read this user
if ( !$repository->canUser( 'content', 'read', $user ) )
{
    // Generates message: User does not have access to 'content' 'read' with id '10'
    throw new \eZ\Publish\Core\Base\Exceptions\UnauthorizedException( 'content', 'read', [ 'id' => $userId ] );
}
```

### Command line

Output must always be escaped when displaying data from the database.

## Data and databases

- Values coming from variables should always be appropriately quoted or binded in SQL statements
- The SQL statements used should never be created by hand with one version per supported database, as this increases both the maintenance load and the chances for security-related problems
- Usage of temporary tables is discouraged, as their behavior is very different on different databases. Subselects should be preferred (esp. since recent mysql versions have much better support for them)
- Full table locking is discouraged

### Sessions

- Business logic should not depend on database connections being either persistent or not persistent
- The connection to the database should always be opened as late as possible during page execution. Ideally, to improve scalability, a web page executing no queries should not connect to the database at all (note that closing the database connection as soon as possible is a tricky problem, as we expect to support persistent database connections as well for absolute best performances)
- The same principle applies to configurations where a master/slave database setup is in use: the chance for a failure due to a database malfunction should not increase with the number of database servers at play, but actually decrease
- It is recommended to avoid as much as possible statements which alter the current session, as they slow down the application, are brittle and hard to debug.
    Point in case; if a database session locks a table then is abruptly terminated, the table might stay locked for a long time

### Transactions

- Transactions should always be used to wrap sql statements which affect data in multiple tables: either all data changes go through or none of them
- Transactions are prone to locking issues, so the code executed within a transaction should be limited to the minimum necessary amount (ex. clearing caches should be done after the transaction is committed)
- When using transactions, always consider side effects on external system, such as on-disk storage. E.g. is a transaction relative to creating an image variation is rolled back, the corresponding file should not be left on disk
- Nested transactions are supported in the following way:
    - a transaction within another one will not commit when requested, only the outermost transaction will commit
    - a transaction within another one will roll back all the way to the start of the outermost transaction when requested
    - as a result a transaction shall never be rolled back just as a means of cancelling its work - the side effect might be of cancelling other work which had just been done previously

### Limitations in the SQL dialect supported

Striving to support Mysql 5, PostgreSQL xx and Oracle 10, the following limitations apply:

- Tables, columns and other database objects should not use names longer than 30 chars
- Varchar columns with a definition of *default "" not null* are discouraged
- For SELECTs, offset and limit have to be handled by the php layer, not hardcoded in the SQL
- Never treat a NULL varchar value as semantically different from an empty string value
- The select list of a query cannot contain the same field multiple times
- For GROUP BY statements, all fields in the group by clause should be in the select list as well
- For SELECTs, usage of the AS token is allowed in the select list, but not in the list of tables
- Do not put quotes around numeric values (use proper casting/escaping to avoid SQL injection)
 
