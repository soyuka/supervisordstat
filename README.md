# SupervisordStat

Run [`pidstat`](http://sebastien.godard.pagesperso-orange.fr/man_pidstat.html) over supervisord processes. 

## Usage

The process will log to a file named `monit-{process-name}.txt`. To transform the output to csv and json, one may use csvtk:

```
#!/bin/bash

# Usage to-csv.sh monit-logger.txt

temp=${1/txt/temp}
tab=${1/txt/tab}
csv=${1/txt/csv}
json=${1/txt/json}
head -n 3 $1 | tail -n 1 | sed 's/# //' > $temp
sed '1d' $1 >> $temp
csvtk space2tab $temp > $tab
csvtk tab2csv $tab > $csv
csvtk csv2json $csv > $json
rm $temp $tab
```

## Dockerfile

```
FROM golang:1.11-alpine AS build-env
RUN apk add --update git && rm -rf /var/cache/apk/*
ADD supervisordstat /src
RUN cd /src && go get && CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o supervisordstat .

FROM xxx:xx-alpine
RUN apk add --no-cache --update supervisor sysstat
COPY --from=build-env /src/supervisordstat /usr/bin/supervisordstat
```
