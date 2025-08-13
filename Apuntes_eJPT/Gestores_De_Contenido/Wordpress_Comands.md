Script nmap para enumerar plugins y directorios wordpress 
````
nmap -p80 --script http-wordpress-enum --script-args 'limit=0' <taregt>

````
