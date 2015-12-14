---
layout: post
title:  "Minimal example of Roda-Sequel app connecting to different databases"
date:   2015-12-04 21:22:49
comments: true
author: Přemysl Donát
---
# Minimal example of Roda-Sequel app connecting to different databases

I have a multi-tenant todo app where every user gets his own database for his todos. And i decided i will store client credentials in another separate database for better user management.

So basically i need to:

1. Connect to multiple different todo databases with same schema
2. Connect to one another database with different schema from todo databases

I'v been strugling to implement it in the app itself. I'v experienced that in such scenarios it's good to first implement minimal working example to really find out how it should be.

So here it is:

{% highlight ruby %}
require 'roda'
require 'sequel'

USER_DB = Sequel.sqlite('user_db')

TASK_DB = Sequel.sqlite('first_db', servers: {})
TASK_DB.extension :arbitrary_servers
TASK_DB.extension :server_block

class User < Sequel::Model(USER_DB[:users])
end

class Task < Sequel::Model(TASK_DB[:tasks])
end

class DualDb < Roda
  route do |r|
    r.root do
      db = r.params['db']

      email = User.first.email

      task = TASK_DB.with_server(database: db) do
        TASK_DB.synchronize do
          Task.first.description
        end
      end

      "#{email} - #{task}"
    end
  end
end

run DualDb.app
{% endhighlight %}

Now in the same directory create sqlite3 database 'user_db' with 'users' table with 'email' column. For the tasks databases create 'first_db' and 'second_db' with 'tasks' table and 'description' column and fill it with some different data.

If you then run the rack app you can switch between task databases by using 'db' url param.

Have a good one!
