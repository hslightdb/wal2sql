[![Coverity Scan Build Status](https://scan.coverity.com/projects/4832/badge.svg)](https://scan.coverity.com/projects/wal2sql)

Introduction
============

**wal2sql** is an output plugin for logical decoding. It means that the plugin have access to tuples produced by INSERT and UPDATE. Also, UPDATE/DELETE old row versions can be accessed depending on the configured replica identity. Changes can be consumed using the streaming protocol (logical replication slots) or by a special SQL API.

**format version 1** produces a JSON object per transaction. All of the new/old tuples are available in the JSON object. Also, there are options to include properties such as transaction timestamp, schema-qualified, data types, and transaction ids.

**format version 2** produces a JSON object per tuple. Optional JSON object for beginning and end of transaction. Also, there are a variety of options to include properties.


Configuration
=============

lightdb.conf
---------------

You need to set up at least two parameters at postgresql.conf:

```
wal_level = logical
#
# these parameters only need to set in versions 9.4, 9.5 and 9.6
# default values are ok in version 10 or later
#
max_replication_slots = 10
max_wal_senders = 10
```

After changing these parameters, a restart is needed.

Parameters
----------

* `include-xids`: add _xid_ to each changeset. Default is _false_.
* `include-timestamp`: add _timestamp_ to each changeset. Default is _false_.
* `include-schemas`: add _schema_ to each change. Default is _true_.
* `include-types`: add _type_ to each change. Default is _true_.
* `include-typmod`: add modifier to types that have it (eg. varchar(20) instead of varchar). Default is _true_.
* `include-type-oids`: add type oids. Default is _false_.
* `include-domain-data-type`: replace domain name with the underlying data type. Default is _false_.
* `include-column-positions`: add column position (_pg_attribute.attnum_). Default is _false_.
* `include-origin`: add origin of a piece of data. Default is _false_.
* `include-not-null`: add _not null_ information as _columnoptionals_. Default is _false_.
* `include-default`: add default expression. Default is _false_.
* `include-pk`: add _primary key_ information as _pk_. Column name and data type is included. Default is _false_.
* `pretty-print`: add spaces and indentation to JSON structures. Default is _false_.
* `write-in-chunks`: write after every change instead of every changeset. Only used when `format-version` is `1`. Default is _false_.
* `include-lsn`: add _nextlsn_ to each changeset. Default is _false_.
* `include-transaction`: emit records denoting the start and end of each transaction. Default is _true_.
* `include-unchanged-toast` (deprecated): Don't use it. It is deprecated.
* `filter-origins`: exclude changes from the specified origins. Default is empty which means that no origin will be filtered. It is a comma separated value.
* `filter-tables`: exclude rows from the specified tables. Default is empty which means that no table will be filtered. It is a comma separated value. The tables should be schema-qualified. `*.foo` means table foo in all schemas and `bar.*` means all tables in schema bar. Special characters (space, single quote, comma, period, asterisk) must be escaped with backslash. Schema and table are case-sensitive. Table `"public"."Foo bar"` should be specified as `public.Foo\ bar`. LightDB fork support regexp.
* `add-tables`: include only rows from the specified tables. Default is all tables from all schemas. It has the same rules from `filter-tables`. LightDB fork support regexp.
* `filter-msg-prefixes`: exclude messages if prefix is in the list. Default is empty which means that no message will be filtered. It is a comma separated value.
* `add-msg-prefixes`: include only messages if prefix is in the list. Default is all prefixes. It is a comma separated value. `wal2sql` applies `filter-msg-prefixes` before this parameter.
* `format-version`: defines which format to use. Default is _1_.
* `actions`: define which operations will be sent. Default is all actions (insert, update, delete, and truncate). However, if you are using `format-version` 1, truncate is not enabled (backward compatibility).

Examples
========

There are two ways to obtain the changes (JSON objects) from **wal2sql** plugin: calling functions via SQL or ltdts_recvlogical.
see https://www.cnblogs.com/zm24/p/16434994.html, http://www.light-pg.com/docs/lightdb/current/app-ltdts-recvlogical.html.

License
=======

> Copyright (c) 2013-2022, Euler Taveira de Oliveira
> All rights reserved.

> Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

> Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

> Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

> Neither the name of the Euler Taveira de Oliveira nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

> THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
