My personal site. Uses [Jekyll](https://jekyllrb.com/), a Ruby static site generator.

## Run it
```
bundler
bundle exec jekyll serve
```
Browse to http://localhost:4000

## Deploy
* Find the S3 bucket in the AWS console.
* Delete everything in the bucket.
* Upload the files and folders from this repo's _site directory. Remember to remove the README.md files!

## Notes
Some projects were brought in as submodules.
This was done by going to desired parent directory and running `git submodule add <repo-url>`.