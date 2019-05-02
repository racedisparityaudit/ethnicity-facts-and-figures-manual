## Static site build

### Building the static site

The build process generates a completely static website suitable for hosting on S3 or similar.

#### Starting the build process

The static site build process can be run manually via the following script:

```bash
./manage.py force_build_static_site
```

In order to initiate builds automatically in response to user actions in the Publisher (eg publishing a new page), a
`build` table acts as a basic job queue. New rows are added to this table whenever a build is requested.

To actually run these requested builds, the following script needs to be run at regular intervals:

```bash
./manage.py build_static_site
```

This will check for any requested builds in the `build` table, and if there is one, setting that row’s `status` to
`STARTED`. When the job is finished, the status will be updated to `DONE`, and a timestamp set in the `succeeded_at`
column. If the build fails for any reason, the `status` will set to `FAILED`, a timestamp set in the `failed_at` column,
 and a full stack trace saved to the `failure_reason` column.

If there is more than one requested build in the the table, then only the most-recently added one will be run, and the
status of the other rows set to `SUPERSEDED`.

As well as builds being requested whenever a page is published, they can also be requested manually via the following
script. This can also be run as a post-deploy step.

```bash
./manage.py request_static_build
```

#### Running the build

The build process consists of the following steps:

##### 1. The website is saved to the local filesystem

The website, include all HTML files, images, CSS, javascript and CSV files, is saved as static files within a folder
with a name based on the current timestamp. This folder will be generated at the location specified by the
`STATIC_BUILD_DIR` environment variable (note: this must be a full filesystem path, eg `/app/site`).

##### 2. The website is pushed to a remote Git repo (optional)

The static site will be pushed to Github once the local build completes if the environment variable `PUSH_SITE`
is set to `True`.

In production this step acts as a kind of backup, and provides a full history of every change to the public website.

The following environment variables will need to be set for this step to work:

* `PUSH_SITE`: This should be set to `True`
* `GITHUB_URL`: This should be the name of the Git host and the organisation (or user), eg `github.com/myusername`, but not the actual repo name
* `GITHUB_ACCESS_TOKEN`: This should be an OAuth access token which has pull and push permission to the repository
* `HTML_CONTENT_REPO`: This should be the name of the actual Git repo you'd like to save the content to, eg `static-website`

##### 3. The website is pushed to a remote Amazon S3 bucket (optional)

The static site will be pushed to S3 once the local build completes if the environment variable `DEPLOY_SITE`
is set to `True`.

This step enables the static files to be served by S3 as an actual website (either directly via S3 static hosting), or
via Cloudfront using S3 as a source.

The following environment variables will need to be set for this step to work:

* `DEPLOY_SITE`: This should be set to `True`
* `S3_REGION`: The name of the S3 region, eg `eu-west-2`
* `S3_STATIC_SITE_BUCKET`: The bucket name
* `AWS_ACCESS_KEY_ID`: An AWS access key with permissions to access the S3 bucket
* `AWS_SECRET_ACCESS_KEY`: An AWS secret access key for the above access key

