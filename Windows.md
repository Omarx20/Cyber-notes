# 1: navivigating with terminal
ls,dir : listing the content of the dir, 
get--childitem <path> -force : make you able to look for the hiiden files,
cd: change irectory
---------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 2: premmisions and users with terminal
new-localuser -name "<username>" -fullname "<fullusername>" -description "<description>" -password "<userpassword>"

$<variable> = <value>

read-host -AsSecureString : for reading your input and return it as secure string (can use it for secure passwords)

remove=localuser -name "<username>" : for deleting a user

get-localgroupmember -name <groupname> : show group members

add-localgroupmember -group "<groupname>" -member "<username>" : adding a user to a group

mkdir "<dirname>" : making a dir

new-item "<filename>" : making a file

icacls <dirorfileanme> : show members and their premmisions, --% /grant <username>:<userpremmision>,  

I(inheritance)[OI(files),CI(folers)]_f(fullcontrol)_r(read)_w(write)_m(modify)

msinfo /report <filename> : make a report of system nformation nto a file

explorer : executing windows ecxplorere
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 3: windows programs
1.computer management
2.task manager
3.system configuration
4.system information
5.control panel
6.task scheduler
                   
