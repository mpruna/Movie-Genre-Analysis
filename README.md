# Determine movie Ratings based on genre

### Project Steps

- [ ] Develop movies DB locally
- [ ] Analyze the Dataset
- [ ] Choose **ML-Algorithm** & provide an answer
- [ ] Move all to cloud


### Project Structure

```buildoutcfg
├── datasets
│   ├── genome-scores.csv
│   ├── genome-tags.csv
│   ├── links.csv
│   ├── movies.csv
│   ├── moviesDB.sql
│   ├── moviesDB.sql.tar.gz
│   ├── ratings.csv
│   └── tags.csv
├── README_Dataset.txt
├── README.md
└── read_movies_data.ipynb
```

### Architecture constraints

* Will use an SQL DB in the cloud for example AWS
* Import datasets into a normalized DB
* Create a python/program to interact with the Dataset and provide a solution based on **ML-Algorithm**

Datasets for this project are available at this [link](https://grouplens.org/datasets/movielens/)

MovieLens 20M movie ratings. Stable benchmark dataset. 20 million ratings and 465,000 tag applications applied to 27,000 movies by 138,000 users. Includes tag genome data with 15 million relevance scores across 1,129 tags. Released 12/2019 


### Summary

This dataset (ml-25m) describes 5-star rating and free-text tagging activity from MovieLens, a movie recommendation service. It contains 25000095 ratings and 1093360 tag applications across 62423 movies. These data were created by 162541 users between January 09, 1995 and November 21, 2019. This dataset was generated on November 21, 2019.
Users were selected at random for inclusion. All selected users had rated at least 20 movies. No demographic information is included. Each user is represented by an id, and no other information is provided.
The data are contained in the files genome-scores.csv, genome-tags.csv, links.csv, movies.csv, ratings.csv and tags.csv. More details about the contents and use of all these files follows.
This and other GroupLens data sets are publicly available for download at http://grouplens.org/datasets/

### Usage License

Neither the University of Minnesota nor any of the researchers involved can guarantee the correctness of the data, its suitability for any particular purpose, or the validity of results based on the use of the data set. The data set may be used for any research purposes under the following conditions:

```
The user may not state or imply any endorsement from the University of Minnesota or the GroupLens Research Group.
The user must acknowledge the use of the data set in publications resulting from the use of the data set (see below for citation information).
The user may not redistribute the data without separate permission.
The user may not use this information for any commercial or revenue-bearing purposes without first obtaining permission from a faculty member of the GroupLens Research Project at the University of Minnesota.
The executable software scripts are provided "as is" without warranty of any kind, either expressed or implied, including, but not limited to, the implied warranties of merchantability and fitness for a particular purpose. The entire risk as to the quality and performance of them is with you. Should the program prove defective, you assume the cost of all necessary servicing, repair or correction.
```


### Create movie DB locally:

1. Create a DB user
2. Create a movies database
3. Grant all privileges to that user

### Create grants refs:
* https://mariadb.com/kb/en/library/create-user/
* http://www.daniloaz.com/en/how-to-create-a-user-in-mysql-mariadb-and-grant-permissions-on-a-specific-database/



```buildoutcfg
MariaDB [(none)]> CREATE USER adm_movies IDENTIFIED BY '#####';
Query OK, 0 rows affected (0.003 sec)

MariaDB [(none)]> create database movies;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> use movies;
Database changed

MariaDB [movies]> GRANT USAGE ON *.* TO 'adm_movies'@localhost IDENTIFIED BY '####';
Query OK, 0 rows affected (0.003 sec)
```

### Create DB

We can either create a DB using a python script or mysql shell.
After DB creation we can import the records from *.csv using mysql utility or by using pandas dataframe with
sqlalchemy.

### Create tables 

```buildoutcfg
create table links(
  movieid integer not null,
  imdbid  integer,
  tmdbid  integer,
  primary key (
    movieid
  )
);

create  table movies(
  movieid integer not null,
  title   nvarchar(255),
  genres  nvarchar(255),
  primary key (
    movieid
  )
);

create  table ratings(
  userid    integer not null,
  movieid   integer not null,
  rating    decimal,
  timestamp integer,
  primary key (
    userid,
    movieid
  )
);

create  table tags(
  userid    integer not null,
  movieid   integer not null,
  tag       nvarchar(255)  not null,
  timestamp integer,
  primary key (
    userid,
    movieid,
    tag
  )
);
```

### Load data INFILE

```buildoutcfg
LOAD DATA LOCAL INFILE '/home/Github/Movie-Genre-Analysis/datasets/ratings.csv' 
    -> INTO TABLE ratings
    -> FIELDS TERMINATED BY ',' 
    -> LINES TERMINATED BY '\n'
    -> IGNORE 1 ROWS;
Query OK, 25000095 rows affected, 65535 warnings (7 min 23.100 sec)
Records: 25000095

LOAD DATA LOCAL INFILE '/home/Github/Movie-Genre-Analysis/datasets/tags.csv' 
    -> INTO TABLE tags
    -> FIELDS TERMINATED BY ',' 
    -> LINES TERMINATED BY '\n'
    -> IGNORE 1 ROWS;
Query OK, 1093358 rows affected, 65535 warnings (9.028 sec)
Records: 1093360  Deleted: 0  Skipped: 2  Warnings: 1093853
```