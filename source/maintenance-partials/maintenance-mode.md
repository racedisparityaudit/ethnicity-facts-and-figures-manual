There are times when we might want to put the publisher into maintenance mode, e.g. to run some data migrations that are not possible while users are active. Or we may want to take the static site offline, e.g. because the site has been defaced.

We maintain two static templates that cover these two requirements:

`application/static/errors/site_maintenance.html` is served by Heroku when we toggle the Publisher into maintenance mode.
`application/static/errors/service_unavailable.html` is served by Heroku when the Publisher fails to provide a response to a request, and when we take the static site offline.

See the [Runbook](runbook.html#runbook) for guidance on how to [put the publisher in maintenance mode](runbook.html#putting-the-publisher-in-maintenance-mode) or [make the static site unavailable](runbook.html#making-the-static-site-unavailable).
