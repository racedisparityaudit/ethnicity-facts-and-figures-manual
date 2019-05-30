## Creating a redirect

1. With the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) installed, run:
    * `heroku run -a <app> ./manage.py add_redirect_rule --from-uri <from_uri> --to-uri <to_uri>`
        * `<app>` is the name of the Heroku app, e.g. `rd-cms-production`
        * `<from_uri>` is the path prefix that should be redirected
        * `<to_uri>` is the path prefix that should replace the `<from_uri>` prefix
2. Go to the `/redirects` page on Publisher for the environment you've created a redirect for
    * e.g. `https://publisher.ethnicity-facts-figures.service.gov.uk/redirects` for the production environment
3. Copy the raw XML generated on this page
    * Note: it may help to view the page source, if your browser has tried to pretty-print the XML
4. Go to the [AWS Console](http://console.aws.amazon.com/) and log in with your RDU user account
5. Go to the [S3 console](https://s3.console.aws.amazon.com) and go into the static site bucket(s) for the relevant environment
    * Note: for environments where we have S3 bucket replication set up, make sure to set redirects separately on each bucket
6. Go to the `Properties` tab and open the `Static website hosting` card, paste the redirect rules into the relevant textarea and click `Save`
7. [Recommended]: Clear the [CloudFront](https://console.aws.amazon.com/cloudfront/home) cache by creating an invalidation on the environment's distribution:
    1. Click on the ID of the distribution
    2. Go to invalidations
    3. Create an invalidation for `/*`

<div class="govuk-warning-text">
    <span class="govuk-warning-texticon" aria-hidden="true">!</span>
    <strong class="govuk-warning-text_text">
        <span class="govuk-warning-text_assistive">Warning</span>
        <p>The redirected pages must be available at the new URI before adding the redirect rules to S3.</p>
    </strong>
</div>
