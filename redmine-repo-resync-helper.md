REDMINE REPO RESYNC HELPER
==========================

Download: [here](/utils/redmine-repo-resync-helper).

Type: regular php application with no dependencies.

Author: Artem Butusov <art.sormy@gmail.com>

License: LGPL-2.1

Usage
-----

This application remove changeset information from redmine database so updates in repo can be
loaded again in redmine database.

This can be used for:

- revision message change hook
- resync whole repo

```
Redmine repo resync helper (c) Sormy
Usage: redmine-repo-resync-helper <parameters>
Parameters:
  -d <dsn>      Database PDO DSN (required)
  -u <username> Database username
  -p <password> Database password
  -n <repo>     Repo name (required)
  -r <revision> Repo revision
  -h            Print this screen
  -v            Be verbose
```

Revision message change hook
----------------------------

An example based on subversion, we assume that you have subversion on the same server with
redmine database on mysql.

- Rename @{path_to_repo}/hooks/post-revprop-change.tmpl@ to @{path_to_repo}/hooks/post-revprop-change".
- Edit @{path_to_repo}/hooks/post-revprop-change":

```
#!/bin/sh

REPOS="$1"
REV="$2"
USER="$3"
PROPNAME="$4"
ACTION="$5"

redmine-repo-resync-helper -d mysql:dbname={database} -u {username} -p {password} -n $(basename $REPOS) -r $REV 1>&2 || exit 1
curl http://{domain}/sys/fetch_changesets?key={key} 1>&2 || exit 1

exit 0
```

- Run @chmod +x {path_to_repo}/hooks/post-revprop-change@ to allow execution of script

Resync whole repo
-----------------

- Clear redmine repo: @redmine-repo-resync-helper -d mysql:dbname={database} -u {username} -p {password} -n {repo}@
- Run repo resync: curl http://{domain}/sys/fetch_changesets?key={key}
