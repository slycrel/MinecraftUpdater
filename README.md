# fork details
I've forked this to modify launching minecraft from my init.d script I already set up, used at boot of my server machine and now also for updating the server.  The script is as follows:
```
$> cat /etc/init.d/minecraft 
#!/bin/bash
case "$1" in
  start)
    su -l jeremy -c "screen -S minecraft /home/jeremy/minecraft/mcServer/launch-minecraft.sh"
    echo "Server started on screen minecraft"
    ;;
  stop)
    screen -X -S minecraft kill
    echo "Server shutting down"
    ;;
  *)
    echo "Usage: /etc/init.d/minecraft {start|stop}"
    exit 1
    ;;
esac
```

and my launch-minecraft.sh contains this:
```
$> cat launch-minecraft.sh 
#!/bin/sh
parentdir=${0%/*}
cd "$parentdir"
#java -Xmx6G -Xms6G -jar minecraft_server.jar nogui
java -Xms6912M -Xmx6912M -XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -jar minecraft_server.jar nogui
```

Additionally, I am not a python guy, I also had to run `sudo apt install python-requests` for the script to work properly, as the proper dependencies are not installed with python by default.

# MinecraftUpdater
This is a python package to automate the updating of your server. Its so annoying to try and download the jar,
ftp it over, stop the server, back up your world, etc. This automates alll that. just git clone this in the root of
your server so there is an extra folder. Then run python update.py in the new folder. it will check if you have the
latest version. If not if will download the latest jar, then using screen it will announce to the server that it will
shutdown and give a 30 seconds countdown before stopping the server. it will then backup your world into a new folder
when it updates incase something goes wrong. then update the server jar and start the server back up in screen so its in the background.
           
## Configuration

### Latest vs. Snapshot
UPDATE_TO_SNAPSHOT = <True,False>

### Backup Directory
BACKUP_DIR = <name of directory to save files>

### Log File
LOG_FILENAME = <name of file to save log messages>
                
## Scheduling Updates
This script is intended to be run as a cron job.
