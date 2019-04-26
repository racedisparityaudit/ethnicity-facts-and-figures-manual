## Architecture diagram
![alt text](/images/eff-architecture.svg "High-level architecture of the Ethnicity Facts and Figures website and Publisher showing the relationships between components in AWS, Heroku and Github.")

[View/edit source file for architecture diagram](https://docs.google.com/presentation/d/1lbRGSTxkQN9C7blpIPZq0dhgOrgrEVeKU5DJiJp_HQw/edit)

## The Ethnicity Facts and Figures website

The public-facing [Ethnicity Facts and Figures site](https://www.ethnicity-facts-figures.service.gov.uk/) is a set of 
static pages in an S3 bucket (actually two S3 buckets - a primary one in London and secondary mirror in Ireland) which 
are served through a Cloudfront distribution.  The staging static site is set up in the same way, but with an additional
AWS Lambda function to provide basic auth (this is intended to prevent indexing by search engines).

The [style guide](https://guide.ethnicity-facts-figures.service.gov.uk/) is a [GitHub Pages](https://pages.github.com/)
site that is set up to serve from the same domain.

DNS records for `ethnicity-facts-figures.service.gov.uk` are managed in Amazon Route 53.

## The Publisher application

The Publisher is a [Python](https://www.python.org/) web application based on the 
[Flask web framework](http://flask.pocoo.org/) backed by a [Postgres](https://www.postgresql.org/) database.

The Publisher app is hosted on Heroku and released through a three-stage pipeline:

1. **Review app** When a pull request is opened on the Publisher in GitHub the tests are run and a review app is created
   in Heroku.
   Review apps get a database copied from the Staging database, but with at most two measure pages per subtopic
   (review app databases are currently limited by Heroku to 10,000 rows, so we can't use a full copy).

2. **Staging** When a pull request is merged into the `master` branch the tests are run again and the latest `master`
   code is automatically deployed to the Staging environment.
   Staging has a full copy of the production database, but with all user emails and passwords removed.  There is a set
   of user accounts set up for each different role.

3. **Production** Promoting code to production is a manual step: use the "Promote to production..." button in the 
   Heroku pipeline.  This will immediately release the master branch code to production, with no further test run.

## Static site build

The Publisher app allows users to create, update, edit and preview pages for the Ethnicity Facts and Figures site. 
For these changes to be reflected in the pages of the live site they need to be built and pushed to the production S3
bucket. A build of the entire site is requested by Publisher whenever a page is marked as published (or, in rare cases,
unpublished). The request for a build is stored in the `build` table in the database.
 
The static build is run by a Heroku scheduled task which spins up a new instance of the Publisher application within the
environment (which is not accessible on any URL) and runs the `build_static_site` management command. This command first
checks if there are any builds pending in the `build` table, and if there are it builds the whole site into a folder and
(optionally, depending on environment variables) copies the the entire folder to S3 and/or pushes the folder to a 
remote Git repository.

Site builds in Production go to the production bucket, and to the `ethnicity-facts-and-figures-build-history` repository
on GitHub.  This acts as a backup of the entire site (so if S3 became unavailable for some reason it could theoretically
be deployed elswhere), and a history of all published versions of the site.

## Scheduled tasks in Heroku
### Build management
There are a number of tasks that run in relation to building the static site:

* `build_static_site` runs every 10 minutes and checks if there are any builds pending in the database; if there are it 
  runs the build
* `delete_old_builds` runs overnight and clears out the build table of successfully completed builds
* `report_broken_build` and `report_stalled_build` run hourly and will send an email to notify developers if any builds
  have been marked as failed in the database or if any builds have been running for over half an hour (have stalled).

### Database management
The dashboard pages in the Publisher use data from materialized views, and the `refresh_materialized_views` command 
runs nightly to keep these views up to date. (The views are also refreshed right before every static build of the site, 
so that dashboards published on the live site are always up-to-date.)

We keep the data in our Staging database up-to-date by running the `pull_prod_data` command nightly.  This copies data
from the production database into the staging database, removing all user email addresses and passwords and setting up
some known user accounts that can be used for testing in Staging and on review apps.

## Heroku add-on services
### Email
We use Mailgun for sending emails from Publisher.  The only emails we send from the app are for users to confirm new 
accounts and to reset forgotten passwords. 

### Attachment scanning
Users of the Publisher can upload data files for pages on the website, which users of Ethnicity Facts and Figures can
download.  We use Heroku's AttachmentScanner add-on to scan all uploaded files for viruses.

### Monitoring and alerting

Our logs are sent to [Papertrail](https://papertrailapp.com/) where they are aggregated and made searchable. We are 
currently on a package where logs are searchable online for a week, but are archived and downloadable for a year. 
Papertrail allows alerts to be triggered by log events, either by email or as a Slack message. We have alerts set up 
for any platform errors that are detected, for new pages being published, and for login attempts to our production 
database.

We have [Sentry](https://sentry.io) to monitor application errors: and uncaught exceptions (500 errors) are picked up 
by Sentry, which allows issue tracking and captures a detailed stack trace and details of the failed request.

We also have [Sqreen](https://www.sqreen.com/) installed inside Publisher, which monitors activity, blocks some 
suspicious requests and reports several key site metrics.

## Architecture decision records (ADRs)

We keep [a record of architecture decisions](https://github.com/racedisparityaudit/ethnicity-facts-and-figures-publisher/tree/master/doc/decisions)
in the form of Lightweight Architecture Decision Records.
