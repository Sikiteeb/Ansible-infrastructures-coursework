# Backup Restoration Process


Run Ansible playbook to install all the necessary configurations, run this command from the ica0002 folder on your machine.  

```ansible-playbook infra.yaml ```

To *restore*  **MySQL** database you will have to run commands on the Sikiteeb-2 where MySQL configuration is made. 

To *restore* the backup from the server, run this command as **backup** user:  

```sudo su - backup```    
``` rm -r /home/backup/mysql/* ```  
```duplicity --no-encryption restore rsync://sikiteeb@backup.maledetta.xtx//home/sikiteeb/mysql /home/backup/restore/mysql```

To *upload* the backup onto the machine, run this command as **root** user:  

```mysql agama < /home/backup/mysql/agama.sql```

To *restore* **InfluxDB** database you will have to run commands as root on Sikiteeb-3 where InfluxDB configuration is made.

To *restore* the backup from the server, run this command as **backup** user:  

```sudo su - backup```   
```rm -r /home/backup/influxdb/*```  
```duplicity --no-encryption restore rsync://sikiteeb@backup.maledetta.xtx//home/sikiteeb/influxdb/ /home/backup/restore/influxdb```

To *restore* the backup you will need to delete existing Telegraf database first. Stop the Telegraf, so that it does not recreate the database before you could restore it and run these commands as **root** user: 

```service telegraf stop```  
```influx -execute 'DROP DATABASE telegraf'```  
```influxd restore -portable -database telegraf /home/backup/restore/influxdb```

After you have verified that backup was restored successfully, run this command from the ica0002 folder on your machine,
it will start the Telegraf again:  

```ansible-playbook infra.yaml```  
