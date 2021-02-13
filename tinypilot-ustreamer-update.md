Login to your TinyPilot, `sudo` to `root` and backup your ustreamer configuration file:

```
ossiemoore@Ossies-MacBook-Air ~ % ssh pilot@tinypilot
Linux tinypilot 5.4.79-v7l+ #1373 SMP Mon Nov 23 13:27:40 GMT 2020 armv7l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Feb 13 00:13:36 2021 from 192.168.4.45

pilot@tinypilot:~# sudo su -

root@tinypilot:~# cp /lib/systemd/system/ustreamer.service /lib/systemd/system/ustreamer.service.bak

root@tinypilot:~# ls -l /lib/systemd/system/ustreamer.service*
-rw-r--r-- 1 root root 443 Feb 13 00:24 /lib/systemd/system/ustreamer.service
-rw-r--r-- 1 root root 443 Feb 13 11:17 /lib/systemd/system/ustreamer.service.bak
```

Now, edit your `ustreamer.service` configuration file and save changes.

```
root@tinypilot:~# nano /lib/systemd/system/ustreamer.service
```

When the editor comes up, update the contents of the file to look as follows. Updates are to add the `--slowdown`, `--quality 80` and I updated the `--drop-same-frames` from `30` to `90`

```
[Unit]
Description=uStreamer - Lightweight, optimized video encoder
After=syslog.target network.target

[Service]
Type=simple
User=ustreamer
WorkingDirectory=/opt/ustreamer
ExecStart=/opt/ustreamer/ustreamer \
  --host 127.0.0.1 \
  --port 8001 \
  --encoder omx \
  --format uyvy \
  --workers 3 \
  --drop-same-frames 90 \
  --persistent \
  --dv-timings \
  --slowdown \
  --quality 80

Restart=always

[Install]
WantedBy=multi-user.target
```

Then hit `ctrl` + `x` to exit the editor, `Y` to save your changes and `enter` to confirm overwriting the file. Now you need to restart the `ustreamer` service.

```
root@tinypilot:~# systemctl daemon-reload && sudo service ustreamer restart
```

