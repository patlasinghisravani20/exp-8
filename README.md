# exp-8
my first repository in github


sudo useradd -m -s /bin/bash user-1
sudo useradd -m -s /bin/bash user-2
sudo useradd -m -s /bin/bash user-3

id user-1
id user-2
id user-3

sudo groupadd s3-support
sudo groupadd ec2-support
sudo groupadd ec2-admin

getent group s3-support ec2-support ec2-admin

sudo mkdir -p /srv/s3-bucket /srv/ec2

echo "quarterly report data" | sudo tee /srv/s3-bucket/report.txt
echo "running" | sudo tee /srv/ec2/i-001.state

ls /srv/s3-bucket
sudo ls /srv/ec2

sudo chown root:s3-support /srv/s3-bucket /srv/s3-bucket/report.txt
sudo chmod 750 /srv/s3-bucket
sudo chmod 640 /srv/s3-bucket/report.txt

sudo chown root:ec2-support /srv/ec2 /srv/ec2/i-001.state
sudo chmod 750 /srv/ec2
sudo chmod 640 /srv/ec2/i-001.state

sudo tee /usr/local/bin/ec2-ctl > /dev/null <<'EOF'
#!/bin/bash
case "$1" in
  describe) cat /srv/ec2/i-001.state ;;
  start) echo running > /srv/ec2/i-001.state; echo "i-001 started" ;;
  stop) echo stopped > /srv/ec2/i-001.state; echo "i-001 stopped" ;;
  *) echo "usage: ec2-ctl describe|start|stop" ;;
esac
EOF

sudo chmod 755 /usr/local/bin/ec2-ctl
sudo /usr/local/bin/ec2-ctl describe

echo '%ec2-admin ALL=(root) NOPASSWD: /usr/local/bin/ec2-ctl' | sudo tee /etc/sudoers.d/ec2-admin

sudo chmod 440 /etc/sudoers.d/ec2-admin

sudo usermod -aG s3-support user-1
sudo usermod -aG ec2-support user-2
sudo usermod -aG ec2-admin user-3

getent group s3-support ec2-support ec2-admin

sudo -n /usr/bin/sudo -n -u user-1 /bin/ls /srv/s3-bucket
sudo -n /usr/bin/sudo -n -u user-1 /usr/bin/cat /srv/s3-bucket/report.txt
sudo -n /usr/bin/sudo -n -u user-1 /bin/ls /srv/ec2

sudo -n /usr/bin/sudo -n -u user-2 /usr/bin/cat /srv/ec2/i-001.state
sudo -n /usr/bin/sudo -n -u user-2 /bin/ls /srv/s3-bucket
sudo -n /usr/bin/sudo -n -u user-2 /bin/sh -c 'echo stopped > /srv/ec2/i-001.state'

sudo -n /usr/bin/sudo -n -u user-3 /usr/bin/sudo /usr/local/bin/ec2-ctl describe
sudo -n /usr/bin/sudo -n -u user-3 /usr/bin/sudo /usr/local/bin/ec2-ctl stop
sudo -n /usr/bin/sudo -n -u user-3 /usr/bin/sudo /usr/local/bin/ec2-ctl start
sudo -n /usr/bin/sudo -n -u user-3 /bin/ls /srv/s3-bucket

getent group s3-support ec2-support ec2-admin

sudo -n /usr/local/bin/ec2-ctl describe

ls -ld /srv/s3-bucket /srv/ec2

sudo -n ls -l /srv/s3-bucket/report.txt /srv/ec2/i-001.state
