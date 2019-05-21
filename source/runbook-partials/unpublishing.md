## Unpublishing a measure version

1. With the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) installed, run the command below to put it
   back in the 'draft' state, removing all attributes which get set during the publishing process
    * `heroku run -a <app> ./manage.py unpublish_measure_version --topic_slug <topic_slug> --subtopic_slug <subtopic_slug> --measure_slug <measure_slug> --version <version>`
        * `<app>` is the name of the Heroku app, e.g. `rd-cms-production`
        * `<topic_slug>` is the URL slug of the topic, e.g. `health`
        * `<subtopic_slug>` is the URL slug of the subtopic, e.g. `exercise-and-activity`
        * `<measure_slug>` is the URL slug of the measure, e.g. `physical-activity`
        * `<version>` is the major.minor version to unpublish, e.g. `1.1`
2. Go to the [AWS Console](http://console.aws.amazon.com/) and log in with your RDU user account
3. Go to the [S3 console](https://s3.console.aws.amazon.com) and go into the static site bucket(s) for the relevant environment
    * Note: for environments where we have S3 bucket replication set up, make sure to set redirects separately on each bucket
4. Navigate to the object path prefix for the given measure.
5. Delete the folder for the given version.
6. [Recommended]: Clear the [CloudFront](https://console.aws.amazon.com/cloudfront/home) cache by creating an invalidation on the environment's distribution:
    1. Click on the ID of the distribution
    2. Go to invalidations
    3. Create an invalidation for `/*`
