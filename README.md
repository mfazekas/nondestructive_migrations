Nondestructive migrations, also known as data migrations, are a alternative kind of Rails migration. The data migrations operate exactly like schema migrations, except instead of running migrations to make changes to your schema (adding fields, dropping fields, adding tables, etc), you run data migrations to manipulate data in your app, enqueue or execute Resque jobs that require long-running processes. This happens in a Rails app for different reasons, usually to clean up or supplement data or architectural changes.

Splitting your data migrations from your schema migrations has a particular benefit of achieving the most consistent zero-downtime deploys you can. I recommend you switch your deployment script to allow you to do two types of deploys: a Zero-downtime deploy (no schema migrations) and Schema Migration deploy. 

This way, you can deploy any non-destructive (data-only) migration with a Zero-downtime strategy, and opt to make destructive (schema) migrations in a normal deployment (maintenance on, run schema changes, boot up new app,  maintenance off).  Data-only migrations can be run while the app is actually running, augmenting what you can achieve with the migration-style shortcuts provided by Rails.

A word of caution: If you find yourself making a lot of data migrations, you might consider if your product/development/business process is too reliant on one-off data importing. It may be that data management tools will help you in the long run. Nonetheless, separating your schema migrations from your data migrations can be a great strategy for modern Rails development.

Data migrations functional EXACTLY like schema migrations except:

1) They live in db/data_migrate instead of db/migrate

2) They timestamps used to record which ones have been run are in a table called data_migrations instead of the normal schema_migrations table

3) You run them using rake data:migrate instead of rake db:migrate


## Installation

Add this to your gemfile.
```ruby
gem 'nondestructive_migrations'
```

After running `bundle install`, run the setup script:
```
rails generate data_migrations:install
```

This will create a *schema* migration that will create the data_migrations table. Now execute that schema migration:

```
rake db:migrate
```

You are now set up and ready to start making data migrations. To create your first migration, create it with a generating using a camal-case description of what your data migration does. 

```
rails generate data_migration UpdatePhoneNumbers
```




## rake data:migrate
Migrate all unmigrated versions.

## rake data:migrate:down VERSION=xxxxxxxxxxx
Migrate down the specified version

## rake data:migrate:up VERSION=xxxxxxxxxxx
Migrate up the specified versions.

## rake data:rollback
Rollback the last version

