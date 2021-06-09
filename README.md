# simplemount
Samba/CIFS Mount helper

This is a helper tool for managing SMB shares that uses config files (default /opt/conf/simplemount/conf) and not commandline arguments.

BASEDIR - /opt/simplemount/ 	 	- where the script/conf sit and thats about it
DEFAULTCONF - /opt/simplemount/conf	- where to put standard shares that most servers will need
		  /opt/simplemount/secrets/	- put your credential files here, make sure the keys are only readable by root though.
      

# Config File
The config file is 3 required vars for mounting, and the rest are just whatever samba options you would give for mount -o

```shell
	# required by script for mounting
	mount_point=/mnt/data
	server=192.168.0.2
	sharename=data


	# optional arguments - see 'man mount.cifs'
	credentials=/opt/simplemount/secrets/my_samba_key
```

## Credential/Secrets File Format
```shell
username=<userid>
password=<password>
domain=<DOMAIN>
```

# Usage
```
$ /opt/simplemount/simplemount
Usage: /opt/simplemount/simplemount [OPTIONS] COMMAND

Commands:
  ls          Displays shares in table
  mount       Mount an SMB share, requires conf
  unmount     Unmount an SMB share, requires conf
  enable      Enables automatic mounting on startup via /etc/fstab
```

# Example 
```
$ ./simplemount ls
FILENAME                           SERVER         SHARENAME  MOUNT_POINT       MOUNTED  ENABLED  OPTIONS
/opt/simplemount/conf/data.conf    192.168.0.2    data      /mnt/data          NO       NO       credentials=/opt/simplemount/secrets/my_samba_key
/opt/simplemount/conf/open.conf    192.168.0.2    public    /mnt/public        NO       NO       rw,guest
/opt/simplemount/conf/photos.conf  192.168.0.2    photos    /mnt/photos/       NO       NO       credentials=/opt/simplemount/secrets/my_samba_key
```
```
$ sudo ./simplemount mount -a
Mounting: //192.168.0.2/data to /mnt/data
Mounting: //192.168.0.2/public to /mnt/public
Mounting: //192.168.0.2/photos to /mnt/photos/
```
```
$ sudo ./simplemount enable -a
Adding to /etc/fstab: //192.168.0.2/data /mnt/data cifs credentials=/opt/simplemount/secrets/my_samba_key 0 0
Adding to /etc/fstab: //192.168.0.2/public /mnt/public cifs rw,guest 0 0
Adding to /etc/fstab: //192.168.0.2/photos /mnt/photos/ cifs credentials=/opt/simplemount/secrets/my_samba_key 0 0
```
```
$ sudo ./simplemount mount -c conf/data.conf
Mounting: //192.168.0.2/data to /mnt/data
```
