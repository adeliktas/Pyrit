# Scaling up: Using a SQL-database as storage #

Using a SQL-database instead of the filesystem will give you some benefits:

  * Real ACID-compliance, backup- and load-balancing-features.
  * Multiple Pyrit-clients can operate on the same database at the same time over the network.
  * Meta- and binary-data are (possibly) stored independent of each other, making the database easier to query and operate on.

Pyrit uses [SQLAlchemy](http://www.sqlalchemy.org/) and can therefore use all kinds of SQL-databases for it's internal storage mechanism: [SQLite](http://www.sqlite.org/) has all the benefits described above (except the network-functionality), [MySQL](http://dev.mysql.com/) and [PostgreSQL](http://www.postgresql.org/) require some setup but provide more features and better scaling. Please refer to [SQLAlchemy](http://www.sqlalchemy.org/)'s documentation for more details about supported databases.

Using a database as storage is extremely easy - all you got to do is to provide an alternative _connection-string_ instead of _'file://'_ that Pyrit uses by default (please refer to the [Wiki](https://github.com/JPaulMora/Pyrit/wiki) for details about the connection-string).

Before using any of these methods you may want to install pip for dependency resolution. 

`sudo apt-get install python-pip` would do for Ubuntu, and `sudo easy_install pip`
for Mac OSX, note you must have [Brew](brew.sh) and **Xcode Tools** installed (just do `xcode-select --install` in Terminal and follow the prompts). 


##SQLite##

The easiest method of implementing a database for Pyrit is using SQLite, note that we do not have to care about creating the database (in the case of SQLite) or any tables within it. Pyrit will take care of this. In the following example, we use a SQLite-database stored in the single file _'mydb.db'_:
```
pyrit -u sqlite:///mydb.db -i dict.gz import_passwords
```

You should get an output very similar to this:

```
Pyrit 0.3.0 (C) 2008-2010 Lukas Lueg http://pyrit.googlecode.com
This code is distributed under the GNU General Public License v3+

Connecting to storage at 'sqlite:///mydb.db'...  connected.
10202 lines read. Flushing buffers... 
All done.
```
##PostgreSQL##

If you plan to use PostgreSQL, there are some dependencies you must install first, for ubuntu you can run:

```
sudo apt-get build-dep python-psycopg2 
sudo pip install psycopg2
```
or on OSX:
```
brew install postgresql
sudo pip install psycopg2 sqlalchemy
```

Setting up a MySQL- or PostreSQL-server is beyond the scope of this tutorial. However, after setting up the database-server, creating a (empty) database and providing the necessary credentials, the required steps in Pyrit are the same as above. 

the basic syntax for connecting is:

pyrit -u `storage-type`://`user`:`password`@`server`/`DB-name`  followed by the usual pyrit commands.

For example, to use the (already created) database _'pyrit'_ on a PostgreSQL-server at 192.168.0.7 with user _'pyrituser'_ and no password, your commandline would look something like this:

```
pyrit -u postgres://pyrituser:@192.168.0.7/pyrit -e linksys create_essid
```
note there is nothing after the `:` since the connection didn’t require a password, this may vary depending on your DB setup.


Finally, to make life a little easier, you can save the default connection-string in Pyrit's configuration-file at _'~/.pyrit/config'_. Change the value of the key _default\_storage_ to a new connection-string and you won't have to supply it every single time.