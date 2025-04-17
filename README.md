# releases-downlader

A simple bash script for downloading releases assets from github

## Dependencies
this a simple bash script that could be running on any linux distro *Tested only on debian*
this script uses **wget** , **curl** and **jq** commands so be sure that they're installed on your machine

## How to use
Download the script -or if you are using a no-display setup you can copy the contentes of the script-

```
chmod u+x releases-downloader

./releases-downloader --help
```

## TODO

1. Add options for providing github token
2. improve parsing and error handling
3. add option for providing owner and repo name only without the whole link
4. improve readability 

