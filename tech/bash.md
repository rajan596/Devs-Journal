## awk


### command to merge 2nd columns values if 1st and 3rd col values match
awk -F, '{a[$1 FS $3]=a[$1 FS $3] FS $2} END {for(i in a) print i "\,\"" substr(a[i],2) "\""}' topologies.csv > result.csv

Input:
a,1,b
a,2,b
a,3,c

Result:
a,b,"1,2"
a,c,"3"

### Kill all process based on ps command
```
ps -ef | grep metrics.sh | awk '{print $2}' > pids.txt
while read -r line; do sudo kill -9 $line; done < pids.txt
```