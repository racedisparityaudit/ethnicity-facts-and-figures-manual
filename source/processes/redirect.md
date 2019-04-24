## Creating a redirect

1. With the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) installed, run:
    * `heroku run -a <app> ./manage.py add_redirect_rule --from-uri <from_uri> --to-uri <to_uri>`.
        * `<app>` is the name of the Heroku app, e.g. `rd-cms-production`
        * `<from_uri>` is the path prefix that should be redirected
        * `<to_uri>` is the path prefix that should replace the `<from_uri>` prefix.
2. Go to the `/redirects` page on Publisher for the environment you've created a redirect for.
    * e.g. `https://publisher.ethnicity-facts-figures.service.gov.uk/redirects` for the production environment.
3. Copy the raw XML generated on this page.
    * Note: it may help to view the page source, if your browser has tried to pretty-print the XML.
4. Go to the [AWS Console](http://console.aws.amazon.com/) and log in with your RDU user account.
5. Go to the [S3 console](https://s3.console.aws.amazon.com) and go into the static site bucket(s) for the relevant environment.
    * Note: for environments where we have S3 bucket replication set up, make sure to set redirects separately on each bucket.
6. Go to the `Properties` tab and open the `Static website hosting` card. Paste the redirect rules into the relevant textarea. Click `Save`.
7. [Recommended]: Clear the [CloudFront](https://console.aws.amazon.com/cloudfront/home) cache by creating an invalidation on the environment's distribution.
    1. Click on the ID of the distribution.
    2. Go to invalidations.
    3. Create an invalidation for `/*`.

<style>
.govuk-warning-text{font-family:nta,Arial,sans-serif;-webkit-font-smoothing:antialiased;-moz-osx-font-smoothing:grayscale;font-weight:400;font-size:16px;font-size:1rem;line-height:1.25;color:#0b0c0c;position:relative;margin-bottom:20px;padding:10px 0}@media print{.govuk-warning-text{font-family:sans-serif}}@media (min-width:40.0625em){.govuk-warning-text{font-size:19px;font-size:1.1875rem;line-height:1.31579}}@media print{.govuk-warning-text{font-size:14pt;line-height:1.15;color:#000}}@media (min-width:40.0625em){.govuk-warning-text{margin-bottom:30px}}.govuk-warning-text_assistive{position:absolute!important;width:1px!important;height:1px!important;margin:-1px!important;padding:0!important;overflow:hidden!important;clip:rect(0 0 0 0)!important;-webkit-clip-path:inset(50%)!important;clip-path:inset(50%)!important;border:0!important;white-space:nowrap!important}.govuk-warning-texticon{font-family:nta,Arial,sans-serif;-webkit-font-smoothing:antialiased;-moz-osx-font-smoothing:grayscale;font-weight:700;display:inline-block;position:absolute;top:50%;left:0;min-width:32px;min-height:29px;margin-top:-20px;padding-top:3px;border:3px solid #0b0c0c;border-radius:50%;color:#fff;background:#0b0c0c;font-size:1.6em;line-height:29px;text-align:center;-webkit-user-select:none;-moz-user-select:none;-ms-user-select:none;user-select:none}@media print{.govuk-warning-texticon{font-family:sans-serif}}.govuk-warning-text_text{display:block;margin-left:-15px;padding-left:65px}
</style>

<div class="govuk-warning-text">
    <span class="govuk-warning-texticon" aria-hidden="true">!</span>
    <strong class="govuk-warning-text_text">
        <span class="govuk-warning-text_assistive">Warning</span>
        <p>The redirected pages must be available at the new URI before adding the redirect rules to S3.</p>
    </strong>
</div>
