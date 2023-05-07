```
$ amass enum -brute -passive -d abercrombie.com | httpx -silent -status-code | tee domain.txt
$ cat domain.txt | grep 200 | cut -d "[" -f1 > urls.txt
```
