# Backup Restore Manual

Run Ansible playbook to install all the necessary configurations, run this command from the ica0002 folder on your machine.  
```ansible-playbook infra.yaml ```

To restore **MySQL** database you will have to run commands on the first virtual machine where our MySQL configuration is made. 

Firstly, In order to restore the backup from the server, run this command as **backup** user:  
```sudo su - backup```    
``` rm -r /home/backup/mysql/restore/* ```  
```duplicity --no-encryption restore rsync://sikiteeb@backup.maledetta.it//home/sikiteeb/mysql/ /home/backup/mysql/restore```

Secondly, In order to upload the restore backup onto the machine, run this command as **root** user:  
```mysql agama < /home/backup/mysql/restore/agama.sql```

To restore **InfluxDB** database you will have to run commands as root on the first virtual machine where our InfluxDB configuration is made.

Firstly, In order to restore the backup from the server, run this command as **backup** user:  
```sudo su - backup```   
```rm -r /home/backup/influxdb/restore/*```  
```duplicity --no-encryption restore rsync://sikiteeb@backup.maledetta.it//home/sikiteeb/mysql/ /home/backup/mysql/restore```

To restore the backup you will need to delete existing Telegraf database first. It also makes sense to stop the Telegraf, so that it does not recreate the database before you could restore it, run these commands from **root** user:  
```service telegraf stop```  
```influx -execute 'DROP DATABASE telegraf'```  
```influxd restore -portable -database telegraf /home/backup/influxdb/restore```

After you have verified that backup was restored successfully, run this command from the ica0002 folder on your machine,
it will start the Telegraf again:  
```ansible-playbook infra.yaml```  