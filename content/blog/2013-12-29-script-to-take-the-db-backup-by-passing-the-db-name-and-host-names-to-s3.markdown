---
author: vishnur66
comments: false
date: 2013-12-29 13:14:23+00:00
layout: post
link: http://www.vishnu-tech.com/2013/12/script-to-take-the-db-backup-by-passing-the-db-name-and-host-names-to-s3/
slug: script-to-take-the-db-backup-by-passing-the-db-name-and-host-names-to-s3
title: Script to take the DB backup by passing the db name and host names to S3
wordpress_id: 267
---

#!/bin/sh
DB=""
HOST=""
printhelp() {

echo "

Usage: sh mysql.sh [OPTION]...
-db, --database The database name which you need to take the backup

-host, --hostname The hostname

-h, --help Display help file

"

}
while [ "$1" != "" ]; do
case "$1" in
-db | --database ) DB=$2; shift 2 ;;
-host | --hostname ) HOST=$2; shift 2 ;;
-h | --help ) echo "$(printhelp)"; exit; shift; break ;;
esac
done

#### BEGIN CONFIGURATION ####
NOWDATE=`date +%Y-%m-%d`
LASTDATE=$(date +%Y-%m-%d --date='1 week ago')

# set dump directory variables
SRCDIR='/tmp/s3backups'
DESTDIR='test'
BUCKET='vishnu-test'

# database access details
USER='root'
#### END CONFIGURATION ####

# make the temp directory if it doesn't exist and cd into it
mkdir -p $SRCDIR
cd $SRCDIR

# dump the selected sql file and upload it to s3
#for DB in $(mysql -h$HOST -u$USER -e 'show databases' | grep -Ev 'mysql|information_schema|performance_schema|Database')
#do
mysqldump -h$HOST -u$USER --single-transaction $DB > $DB.sql
tar -czPf $DB.tar.gz $DB.sql
mv $DB.tar.gz $NOWDATE-$DB.tar.gz
/usr/bin/s3cmd put $SRCDIR/$NOWDATE-$DB.tar.gz s3://$BUCKET/$DESTDIR/ 
#done

# delete old backups from s3
/usr/bin/s3cmd del --recursive s3://$BUCKET/$DESTDIR/$LASTDATE-$DB.tar.gz

# remove all files in our source directory
cd
rm -rf $SRCDIR/*

 SAVE THIS SCRIPT AND RUN 

FOR EXAMPLE:-  sh  mysql.sh -db <give database name> -host <give hostname>
