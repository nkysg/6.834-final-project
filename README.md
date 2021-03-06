6.834-final-project
===================

[Troy Astorino](https://github.com/troyastorino)'s and [Neil Forrester](https://github.com/nforrester)'s final project for 6.834.

## Dependencies

* python3
* pymysql3 (on pip)

## Training on Wikipedia

We trained our algorithm using data from Wikipedia. As Wikipedia has quite a bit
of data, all these operations take a long time.  In other words, be very patient.

### Downloading Wikipedia data

Wikipedia data can be
downloaded from the [Wikimedia dumps](http://dumps.wikimedia.org/enwiki/). There
you can see this list of available dumps and choose which you want to download.
For our training data we used `20130403`. The data can be downloaded as `.sql`
dumps from the MySQL database that WikiMedia sites use internally. For the
training we performed, 4 of the tables must be downloaded.  Replace `<datestr>`
with the label for the data to download, e.g., `20130403`.

* The [page table](http://www.mediawiki.org/wiki/Manual:Page_table): `http://dumps.wikimedia.org/enwiki/<datestr>/enwiki-<datestr>-page.sql.gz`
* The [pagelinks table](http://www.mediawiki.org/wiki/Manual:Pagelinks_table): `http://dumps.wikimedia.org/enwiki/<datestr>/enwiki-<datestr>-pagelinks.sql.gz`
* The [category table](http://www.mediawiki.org/wiki/Manual:Category_table): `http://dumps.wikimedia.org/enwiki/<datestr>/enwiki-<datestr>-category.sql.gz`
* The [categorylinks table](http://www.mediawiki.org/wiki/Manual:Category_table): `http://dumps.wikimedia.org/enwiki/<datestr>/enwiki-<datestr>-categorylinks.sql.gz`

Downloading all these files will take some time.

### Loading the data into a MySQL database

The script `load-db.py` will load the data into a MySQL instance. First, you
need to create a MySQL database to store the file in. We called ours
`wikipedia`. Assuming you've successfully installed MySQL, do the following:

```
$ mysqld_safe & 
$ mysql -u root 
mysql> create database wikipedia;
mysql> exit
```

If desired, in your `my.cnf` file (possibly in `/etc/my.cnf`...running `$ mysql
--help` will tell where it might be) add the following line.
```
innodb_buffer_pool_size=0
```

Now, `load-db.py` depends on one of the scripts in `xmlfileutils`.  So inside
`xmlfileutils` run `make`.  Now you can run `load-db.py` to extract the files
and load them into the MySQL instance quickly.  Run:

```
$ load-db.py <dir>
```

Where `<dir>` is the location where you downloaded the table dumps.

After running `load-db.py`, you should restart the sever to reinitialize some of
the global SQL variables that were changed for the import.  To shut it down, run

```
$ mysqladmin -u root shutdown
```

## Traversing Wikipedia

The traversal algorithm is in `wiki-fetch.py` (so named because it evolved
from code that was originally intended merely to interface with Wikipedia's API).
If you execute the file as written, it will navigate from `Solar storm of 1859`
to `Albert Einstein`. See the bottom of the file for examples of how to call
the more important functions. Documentation is a little thin, as the whole project
has been a little rushed, but hopefully you can get the general idea.
