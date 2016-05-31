#!/bin/bash
# PaaS Automated Backups Script

backup() {

  # pull down environment variables
  scp -i ./.key/id_rsa $1:mysql/env/OPENSHIFT_MYSQL_DB_USERNAME  .
  scp -i ./.key/id_rsa $1:mysql/env/OPENSHIFT_MYSQL_DB_PASSWORD  .
  scp -i ./.key/id_rsa $1:.env/OPENSHIFT_MYSQL_DB_HOST  .
  scp -i ./.key/id_rsa $1:.env/OPENSHIFT_MYSQL_DB_PORT  .

  # set environment variables to script variables
  username="$(cat OPENSHIFT_MYSQL_DB_USERNAME)"
  password="$(cat OPENSHIFT_MYSQL_DB_PASSWORD)"
  host="$(cat OPENSHIFT_MYSQL_DB_HOST)"
  port="$(cat OPENSHIFT_MYSQL_DB_PORT)"

# pull down site database
ssh -i "./.key/id_rsa" $1 << EOF
mysqldump --password=$password -h $host -P $port -u $username --all-databases --add-drop-table --lock-tables=false >  app-root/repo/dbbackup.sql
sleep 15
EOF

  # remove the unneeded environment variable files
  rm OPENSHIFT_MYSQL_DB_USERNAME OPENSHIFT_MYSQL_DB_PASSWORD OPENSHIFT_MYSQL_DB_HOST OPENSHIFT_MYSQL_DB_PORT

  # check and see if our site directory exists if not create it
  if [ ! -d "sites/$2" ]; then
    mkdir sites/$2
  fi

  # pull down site files
  rsync -u --delete -v -r -L -e "ssh -i ./.key/id_rsa" $1:~/app-root/repo ./sites/$2
}

# backup the websites! (example fake sites for reference)
#backup "53f25cdf4382c4ec30002e5@acanadaautoremarketing-sacherokee.rhcloud.com" "arcanadaautoremarketing"
#backup "55db4c940c166cae6000145@alanticgeneraldev-sacherokee.rhcloud.com" "atlanticgeneraldev"
#backup "54a2c789432ec157f00002e@alantictireonline-sacherokee.rhcloud.com" "atlantictireonline"
#backup "552fded8482ec113b000022@banchcomm-sacherokee.rhcloud.com" "branchcomm"
#backup "55a6c2a4382ec2051000038@camarkautofinance-sacherokee.rhcloud.com" "carmarkautofinance"
#backup "55a66025973ca5b6100003d@camarkcertified-sacherokee.rhcloud.com" "carmarkcertified"
#backup "53f25264382ec26b3000752@camarkdealerservices-sacherokee.rhcloud.com" "carmarkdealerservices"
#backup "5416ff74382eceee50009c6@camarkvehicles-sacherokee.rhcloud.com" "carmarkvehicles"

touch sites/success
