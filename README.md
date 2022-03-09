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

# OakAudioStream

    Pi Zero https://thepihut.com/products/raspberry-pi-zero-2

    Zero4U USB HUB hat - https://www.uugear.com/doc/Zero4U_UserManual.pdf

    5G dongle - https://thepihut.com/products/raspberry-pi-dual-band-5ghz-2-4ghz-usb-wifi-nano-adapter

You can download an older version of Raspberry Pi OS which works with the provided driver
https://downloads.raspberrypi.org/raspios_armhf/images/raspios_armhf-2020-05-28/

https://github.com/fastoe/RTL8811CU_for_Raspbian

sudo crontab -e
If it’s the first time you do this, select your favorite text editor
Press enter to stay with nano

In the crontab file, add the following line at the end:
@reboot ifconfig wlan0 down
