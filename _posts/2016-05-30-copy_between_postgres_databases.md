---
layout: post
title:  "Copy data between 2 postgres dbs"
date:   2016-05-30 21:22:49
comments: true
author: Přemysl Donát
---
# Copy data between 2 postgres dbs

~~~
# turns on/off tuples only => means no printing of headers/footers etc, just rows
psql=# \t
# turns on/off aligned output => basically pretty tables or not
psql=# \a
# sets output to some file. Turn off with another "\o"
psql=# \o FILE

# Load dump into currently connected database. Delimiter can be omitted if it's defaut one but i don't know which one is it and dump is delimited with '|'
\COPY table_name (columns) FROM dump_file DELIMITER '|';

~~~

[psql copy docu](https://wiki.postgresql.org/wiki/COPY)

[postgres sql copy docu](https://www.postgresql.org/docs/current/static/sql-copy.html)
