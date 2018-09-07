👻🛤😍

# About GhostMigration  

Make [Rails ActiveRecord migrations](https://edgeguides.rubyonrails.org/active_record_migrations.html) support zero downtime transformations on large tables, via Github's [gh-ost tool](https://github.com/github/gh-ost).

Why do we need this? Because Ghost is arguably more powerful than previous zero-downtime tools (e.g. wrappers for Percona online-schema-change or Large Hadron Migrator). Ghost rebuilds the table just like other tools, but  diverges in its methodology for converging it to synced-up state. Traditional replication tools rely on triggers. Ghost relies on binary logs, which has many advantages in terms of stability and features the tool can offer. [Read more about Ghost's triggerless migrations](https://github.com/github/gh-ost/blob/master/doc/triggerless-design.md). 

# Alpha edition - YMMV Caveat Emptor Here Be Dragons

This is new and limited, no tests, not even a proper gem yet. That said, I've used this in production for some large tables a few times and after all, it's just a wrapper for the sophisticated ghost tool. It's presently targeting transforms only on the primary database right now, not yet built to handle the replica scenario.

#  Usage

* Include GhostMigration somewhere, e.g. in lib/ (will be a gem later)
* Simply inherit from  GhostMigration instead of ActiveRecord::Migration
* Make `up` and (if you want reversibility)`down` commands. 
* Use `add_column`, `remove_column`, `add_index`, `remove_index` as normal. Basic usage of these tasks should work as normal.
* The default settings are optimised for development, ie "happy go lucky" without some of the precautionary tools ghost offers (e.g. it will delete the old table automatically). You can overwrite 

# Notes

* Only one table is supported per migration. You don't need to state the table explicitly as it will be inferred from the migration commands. The underlying ghost tool only supports one table per invocation.
* If you have several changes planned, it's usually recommended to bundle them into the same migration, assuming they apply to a single table. It takes the same amount of time to do all of them if they're performed in one migration. If you spread them across multiple migrations, it may be  safer in some cases, but it will require re-running ghost each time, meaning rebuilding the whole table each time.
* `change` is not supported at this time. Only `up` and `down`.
* You should be able to customise settings using `GhostMigration.flags some_setting: 1, another_setting: 2`, e.g. in a config/initializer/ghost.rb file.  This structure is essentially the command flags passed to ghost- see `ghost_flags` method for more details.

# Wishlist

* Hook into ActiveRecord code base to reuse more of the implementation instead of reconstructing it
* Support more obscure  ActiveRecord parameters
* Replication scenarios
* Test cases. Testing is virtue amirite.
