Setting up Discourse on Heroku
==============================
*Last updated for version 0.8.3*

If you follow this guide, you should be able to run fully-functional Discourse on Heroku for free (up to a point)

[view original README](README_ORIG.md)

Install addons
--------------
`heroku addons:add heroku-postgresql:dev --version=9.2`

might as well use Postgres 9.2 although discourse will work with 9.1, which is Heroku's default at time of writing

`heroku addons:add mandrill:starter rediscloud:20 redistogo:nano scheduler`

mandrill is for email delivery, although the free mailgun or sendgrid plans will work as well (just update the appropriate config vars). rediscloud 20mb is for the cache db, redistogo nano (5mb) is for queueing. Since neither addon allows you to have more than one db, I just added both to keep the databases separate since the cache tends to get flushed a lot.

`heroku labs:enable user-env-compile`

This will enable asset precompilation to complete successfully.

Setup config vars
-----------------
1. `rake secret`
1. copy the output
1. `heroku config:add SECRET_TOKEN=<paste here>`
1. copy your API key from [your Heroku account](https://dashboard.heroku.com/account)
1. `heroku config:add HEROKU_API_KEY=<paste here>`
1. `heroku config:add SMTP_SERVER=smtp.mandrillapp.com SMTP_PORT=587 HEROKU_APP=<heroku_app_name>`

Setup scheduler tasks
---------------------
`heroku addons:open scheduler`

Set up the scheduled tasks as follows:
![scheduler config](http://d.pr/i/FKJj+)

Setup S3 storage for uploads (optional)
---------------------------------------
This is the one non-free area. You can get S3 storage for free if you fall under the free tier for 12 months, but you may have to pay for S3 storage. This should be fairly cheap anyway, all things considered. Run the following:

`heroku config:add AWS_ACCESS_KEY_ID=<your_access_key> AWS_SECRET_ACCESS_KEY=<your_secret_key>`

navigate to `http://<your-app>.herokuapp.com/admin/site_settings`

1. Check the box for `enable_s3_uploads`
2. Paste your bucket into `s3_upload_bucket`