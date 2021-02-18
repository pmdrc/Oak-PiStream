# OakStreamCam

sudo rasp-config
enable ssh, camera
  
**WiFi**
  
Edit /etc/wpa_supplicant/wpa_supplicant.conf
     
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    country=GB
    
    network={
        ssid="xxxxx"
        psk="xxxxx"
        key_mgmt=WPA-PSK
        priority=1
    }
    
sudo /etc/dhcpcd.conf


sudo apt-get install vlc
nano stream-rtsp.sh

    #!/bin/bash
    nice -n -5 raspivid -o - -t 0 -md 1 -w 1280 -h 720 -fps 30 -b 2500000 | nice -n -5 cvlc --no-inhibit  stream:///dev/stdin
    --sout '#rtp{sdp=rtsp://:8554/stream}' :demux=h264 :h264-fps=30

  chmod 711 stream-rtsp.sh
  sudo sed -i 's/geteuid/getppid/' /usr/bin/vlc
  cd /etc/systemd/system/
  sudo  nano stream-rtsp.service

    [Unit]
    Description=auto start stream
    After=multi-user.target
    
    [Service]
    Type=simple
    ExecStart=/home/pi/stream-rtsp.sh
    User=root
    WorkingDirectory=/home/pi
    Restart=on-failure
    
    [Install]
    WantedBy=multi-user.target

  sudo systemctl daemon-reload
  sudo systemctl enable stream-rtsp.service
  sudo service stream-rtsp start
  sudo shutdown -r now



Notes:
1 - **Recovery Mode**
    at boot press shift
    add "init=/bin/bash" to the cmdline.txt
    after boot do "mount -o remount, rw /"
    "passwd pi" or "nano /etc/wpa_supplicant/wpa_supplicant.conf
    boot press shift
    remove "init=/bin/bash" to the cmdline.txt

other notes:
https://hmbd.wordpress.com/2016/08/01/raspberry-pi-video-and-audio-recording-and-streaming-guide/

