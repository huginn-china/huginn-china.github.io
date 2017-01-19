---
title: Getting Started
layout: docs
permalink: /docs/getting-started.html
nextPage: resources.html
---

## Install [Huginn](https://github.com/cantino/huginn)

1. Clone the repo

        $ git clone git@github.com:cantino/huginn.git
        $ cd huginn

2. Install [Ruby](http://www.ruby-lang.org/en/downloads/) and [Ruby Gems](https://rubygems.org/pages/download) (if necessary)
3. Install rake and bundle (if necesssary):

        $ gem install rake bundle

4. Install Huginn's dependencies

        $ bundle install

5. Install [MySQL](http://dev.mysql.com/downloads/)
6. Start the MySQL server:

        $ mysql.server start

6. Copy *.env.example* to *.env*:

        $ cp .env.example .env

8. Create the `APP_SECRET_TOKEN`:

        $ rake secret

7. Edit *.env*, at least updating the `APP_SECRET_TOKEN` variable we just created.
8. Create a development MySQL database with some example seed data:

        $ rake db:create
        $ rake db:migrate
        $ rake db:seed

8. All done. Fire up the local server: 

        $ foreman start  

    Navigate to [http://localhost:3000/](http://localhost:3000/) and login with the username and the password of "admin" and "password", respectively.

