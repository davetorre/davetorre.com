My personal site. Uses [Jekyll](https://jekyllrb.com/), a Ruby static site generator.

## Run it
```
bundler
bundle exec jekyll serve
```
Browse to http://localhost:4000

## Deploy
Merging or pushing to main should build and deploy to S3 via a GitHub action.
Otherwise, you could replace the S3 bucket contents with the contents of the _site directory manually (don't copy the README.md files!).

## Notes
Some projects were brought in as submodules.
This was done by going to desired parent directory and running `git submodule add <repo-url>`.

## Future
Add Cloudfront? Do I need to set `--cache-control` when syncing with the s3 bucket?
