## Putting the publisher in maintenance mode

1. From the [Heroku Dashboard](https://dashboard.heroku.com/teams/racedisparityaudit/apps), select the 'ethnicity-facts-and-figures' deployment pipeline
2. Click on the app you want to put into maintenance mode (e.g. `rd-cms-staging` or `rd-cms-production`)
3. Click on `Settings`
4. Click on the toggle for `Maintenance mode`; the publisher will become unavailable immediately and display the maintenance page (as defined in the app's environment under `MAINTENANCE_PAGE_URL`)
5. When maintenance has finished, toggle `Maintenance mode` again to turn it off; the publisher will be available immediately

## Making the static site unavailable

1. Log into [AWS Console](http://console.aws.amazon.com/) and log in with your RDU user account
2. Go to the [CloudFront](https://console.aws.amazon.com/cloudfront/home?region=eu-west-2) service
3. Go into the settings of the distribution you want to put into maintenance mode (e.g. `Staging www site` or `Prod www site`)
4. Go to the `Behaviours` tab and create a new behaviour
5. Enter `*` as the path pattern, select `s3-error-pages` as the Origin, set Object Caching to `custom` and change the Minimum/Maximum/Default TTL to a low value (e.g. `5` seconds), then click `Create`
6. Go to the `Error Pages` tab and update the `403` entry: make a note of the current `Error Caching Minimum TTL` and `Response Page Path`, and then set them to `5` and `/service_unavailable.html` respectively
7. Create a new invalidation with the path `/*` to purge all cached pages from CloudFront
8. Once the invalidation has completed, CloudFront will serve only the maintenance page for all requests

To make the static site available again:

1. Go to the `Behaviours` tab and delete the behaviour created earlier (with a path pattern of `*` and an Origin of `s3-error-pages`)
2. Go to the `Error Pages` tab and update the `403` entry: restore the previously recorded `Error Caching Minimum TTL` and `Response Page Path`
3. Create a new invalidation with the path `/*` to purge all cached pages from CloudFront
4. Once the invalidation has completed, CloudFront will start serving the static site again
