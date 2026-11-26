## commands ##
* /     : This is a top level directory. it is parent directory for all other directories, it is represent by the forward slash (/) . This is called root directory.
* /root : It is the home directory for the root user (superuser). It provides the working environmen for the root user .
* /home : This is home directory for the other user in linux. It provides a working environmnt for other users (other than root)
* /bin  : It contains commands used by all users (binary files) i.e; ls, cp, mv.
* /boot : The /boot file system contains the linux kernal, boot support files, and boot configurationfiles for linux. Holds files necessary    for booting the OS.
* /dev  : It contains device files like hardDisk:/dev/hda cdrom.
* /etc  : Contains all system level configuration files like /etc/passwd. user info
* /usr  : By default software are installed in /usr directory
* /sbin : 


## Linux Basic Commands ##
* env            : Displays all the environment variable for the user.
* which ls       : ls is in a directory called bin (/usr/bin/ls)
* pwd            : print working directory
* history        : shows the history of commands
* `user          : specified users home directory
* whoami         : shows as what user we are working now
* uname          : prints the system information
* man uname      : tell you all commands
* sudo su        : switch to root user
* ls             : list files and directories from current location
* ls -la/ls -lart: list all files include hidden files also
* ls path        : list from mentioned path (ex: ls /etc/ or /ect/*)
* touch          : create a empty file with given name
* cd             : change directory
* cd -           : goes one step back from current path
* cd path        : takes in to specific path
* cat            : view data from a file (prints the whole file)
* more           : view data from a file ( page by page it will display)
* less           : view data from a file ( we can searth particular word and up bottom we can move)
* head           : display top 10 lines
* tail / -100f   : display bottom 10 lines
* head -n 2 / -5 : display only top 2 lines
* tail -n 2 /-5  : display only bottom 2 lnes
* pr             : gives proper formate while printing (ex: pr config - it will display the file in one page)
* sort           : sort a file alphabetical order
* chown          : to change the ownership
* chmod          : to change the pernissions
* chgrp          : to change the group
* id -u          : tells your id
* id -un         : tells you username
* free -h        : shows free memory in readable formate
* top            : shows the utilization
* df -h          : shows the space
* ps -ef | grep  : shows the running process
* cp -pr         : copy the file or rename the file ( -r(recursive) means in one directory in side all files need to copy , -p(preserve) file permission and ownership , -v (verbose) which files is copying it shows, -i (interactive) in destination same file it will overwrite ask permission )
* mv             : move the file from one to another path or rename the file name
* cal            : shows the calander
* uptime         : shows the uptime
* rm -rf         : delete the file or directory forcefully
* mkdir -p       : create a directory (ex: a/b/c/d..)
* java -version  : shows the java version
* 


* echo "linux" > file.txt
* 





## hostname ##
* hostname       : shows the hostname
* hostname -I    : shows the ip 
* hostnamectl    : 


grep -i "cfaapi" access.log | grep -i "loandetails" | grep -i 'proxyResponseCode":500'
grep -R "cdsambrms.chola.murugappa.com" .
"C:\Program Files\PuTTY\putty.exe" -ssh  ngnix@10.9.20.109 -pw CholaAPMG6$2025
"C:\Program Files\PuTTY\putty.exe" -ssh mgmtapp@10.9.20.172 -P 4022 -pw Chola$2024$june$
find / -type f -name 'losConstants.properties' 2>/dev/null




## word count ##
* wc <filename>  : word count in a file (lines, words, bytes)
* wc -l/-w/-c    : -l lines count -w words count -c bytes count
* wc -l <file>   : only line count
* nl <filename>  : it adds line number
* fmt <filename> : splits file in to multiple lines if having large data


## Nginx install
1 need to download tar file
2 untar go to inside -- 1.28.0
then need to enter this command
./configure \

  --prefix=/usr/local/nginx \
  --with-http_ssl_module \
  --with-http_realip_module \
  --with-http_mp4_module \
  --with-http_gunzip_module \
  --with-http_gzip_static_module \
  --with-http_degradation_module \
  --with-http_stub_status_module \
  --with-pcre
make
sudo make install
after that 
 sudo mkdir -p /node/nginx-logs/logs
/usr/local/nginx/sbin/nginx -t
check syntax
