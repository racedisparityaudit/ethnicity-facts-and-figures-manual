# Technical Documentation

This project uses the [Tech Docs Template][template], which is a [Middleman template][mmt] that you can use to build technical documentation using a GOV.UK style.

## Before you start

To build the Ethnicity facts and figures tech docs locally, you need:

- [Ruby][install-ruby]
- [Middleman][install-middleman]

## Making changes

To make changes to the documentation, edit files in the `source` folder of this repository. We use the [basic multiple structure][basic-multipage]. Each page is declared in the top level of the `source` folder with a single heading. All other content is then included, via [Ruby partials][partials], through markdown files in sub-directories.

## Preview your changes locally

To preview your new website locally, navigate to your project folder and run:

```sh
bundle exec middleman server
```

See the generated website on `http://localhost:4567` in your browser. Any content changes you make to your website will be updated in real time.

To shut down the Middleman instance running on your machine, use `ctrl+C`.

If you make changes to the `config/tech-docs.yml` configuration file, you need to restart Middleman to see the changes.

## Build

To build the HTML pages from content in your `source` folder, run:

```sh
bundle exec middleman build
```

Every time you run this command, the `build` folder gets generated from scratch. This means any changes to the `build` folder that are not part of the build command will get overwritten.

## Deployment
This manual gets automatically deployed to Heroku when changes are merged into the `master` branch.

## Troubleshooting

Run `bundle update` to make sure you're using the most recent Ruby gem versions.

Run `bundle exec middleman build --verbose` to get detailed error messages to help with finding the problem.

## Licence

Unless stated otherwise, the codebase is released under [the MIT License][mit].
This covers both the codebase and any sample code in the documentation.

The documentation is [© Crown copyright][copyright] and available under the terms of the [Open Government 3.0][ogl] licence.

[mit]: LICENCE
[copyright]: http://www.nationalarchives.gov.uk/information-management/re-using-public-sector-information/uk-government-licensing-framework/crown-copyright/
[ogl]: http://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/
[mmt]: https://middlemanapp.com/advanced/project_templates/
[config]: https://tdt-documentation.london.cloudapps.digital/configuration-options.html#configuration-options
[basic-multipage]: https://tdt-documentation.london.cloudapps.digital/multipage.html#basic-multipage
[partials]: https://tdt-documentation.london.cloudapps.digital/single_page.html#add-partial-lines
[install-ruby]: https://tdt-documentation.london.cloudapps.digital/install_macs.html#install-ruby
[install-middleman]: https://tdt-documentation.london.cloudapps.digital/install_macs.html#install-middleman
[gem]: https://github.com/alphagov/tech-docs-gem
[template]: https://github.com/alphagov/tech-docs-template
