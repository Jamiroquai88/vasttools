# How to setup the vast.ai machine
Install Ubuntu 20.04
You will see a message pending for 2+ minutes when booting, to disable open `/etc/netplan/00-installer-config.yaml` and add
```
optional: true
```

Install build tools
```
sudo apt install build-essential
```

Download NVIDIA driver
```
wget https://us.download.nvidia.com/XFree86/Linux-x86_64/525.89.02/NVIDIA-Linux-x86_64-525.89.02.run
```
Install it
```
sudo bash NVIDIA-Linux-x86_64-525.89.02.run
```
Install python2.7
```
sudo apt install python2.7
```
Install vast software


# vasttools

***I am open to offers for assistance in deployment on vast and continued support.*** Find me on Discored Etherion#0700

The aim is to setup a list of usble tools that can be used with vastai.
The tools are free to use, modify and distribute. If you find this useful and wish to donate your welcome to send your donations to the following wallets.

BTC 15qkQSYXP2BvpqJkbj2qsNFb6nd7FyVcou

XMR 897VkA8sG6gh7yvrKrtvWningikPteojfSgGff3JAUs3cu7jxPDjhiAZRdcQSYPE2VGFVHAdirHqRZEpZsWyPiNK6XPQKAg

RVN RSgWs9Co8nQeyPqQAAqHkHhc5ykXyoMDUp

USDT(ETH ERC20) 0xa5955cf9fe7af53bcaa1d2404e2b17a1f28aac4f

Paypal  PayPal.Me/cryptolabsZA



## Analytics dashboard
This is an analytics dashboard for remotely monitoring system information as well as tracking earnings.
https://github.com/jjziets/vastai_analytics_dasboard

## Memory oc

set the OC of the RTX 3090
It requires the folliwing

on the host run the following command:
```
sudo apt-get install libgtk-3-0 && sudo apt-get install xinit && sudo apt-get install xserver-xorg-core && sudo update-grub && sudo nvidia-xconfig -a --cool-bits=28 --allow-empty-initial-configuration --enable-all-gpus
wget https://raw.githubusercontent.com/jjziets/vasttools/main/set_mem.sh
sudo chmod +x set_mem.sh
sudo ./set_mem.sh 2000 # this will set the memory OC to +1000mhs on all the gpus. You can use 3000 on some gpu's which will give 1500mhs OC. 
```

## OC monitor
setup the monitoring programe that will change the memory oc based on what programe is running. it desinged for RTX3090's and targets ethminer at this stage.
It requires both set_mem.sh and ocmonitor.sh to run in the root.

```
wget https://raw.githubusercontent.com/jjziets/vasttools/main/ocminitor.sh
sudo chmod +x ocminitor.sh
sudo ./ocminitor.sh # I suggest running this in tmux or screen so that when you close the ssh connetion. It looks for ethminer and if it finds it it will set the oc based on your choice. you can also set powerlimits with nvidia-smi -pl 350 
```

Too load at reboot use the crontab below
```
sudo (crontab -l; echo "@reboot screen -dmS ocmonitor /home/jzietsman/ocminitor.sh") | crontab -  #replace the user with your user
```

## Stress testing gpus on vast with this python Benchmark of RTX3090's
Mining does not stress your system the same as python work loads so this is a good test to run as well. 
https://github.com/jjziets/pytorch-benchmark-volta

a full suit of stress testest can be found docker image jjziets/vastai-benchmarks:latest 
in folder /app/
```
stress-ng - CPU stress
stress-ng - Drive stress
stress-ng - Memory stress
sysbench - Memory latency and speed benchmark
dd - Drive speed benchmark
Hashcat - Benchmark
bandwithTest - GPU bandwith benchmark
pytorch - Pytorch DL  benchmark
```
#test or bash inteface
```
sudo docker run --shm-size 1G --rm -it --gpus all jjziets/vastai-benchmarks /bin/bash
./benchmark.sh
```
#Run using default settings
Results are saved to ./output.

```
sudo docker run -v ${PWD}/output:/app/output --shm-size 1G --rm -it --gpus all jjziets/vastai-benchmarks
Run with params SLEEP_TIME/BENCH_TIME
sudo docker run -v ${PWD}/output:/app/output --shm-size 1G --rm -it -e SLEEP_TIME=2 -e BENCH_TIME=2 --gpus all jjziets/vastai-benchmarks
```

*based on leona / vast.ai-tools

## Telegram-Vast-Uptime-Bot
This is a set of scripts for monitoring machine crashes. Run the client on your vast machine and the server on a remote one. You get notifications on Telegram if no heartbeats are sent within the timeout (default 12 seconds).
https://github.com/jjziets/Telegram-Vast-Uptime-Bot

## Auto update the price for host listing based on mining porfits.

based on RTX 3090 120Mhs for eth. it sets the price of my 2 host. 
it works with a custom Vast-cli which can be found here https://github.com/jjziets/vast-python/blob/master/vast.py
The manager is here https://github.com/jjziets/vasttools/blob/main/setprice.sh

This should be run on a vps not on a host. do not expose your Vast API keys by using it on the host.
```
wget https://github.com/jjziets/vast-python/blob/master/vast.py 
sudo chmod +x vast.py
./vast.py  set api-key UseYourVasset
wget https://github.com/jjziets/vasttools/blob/main/setprice.sh
sudo chmod +x setprice.sh
```

## Backgorund job or idle job for vast.
![image](https://user-images.githubusercontent.com/19214485/180140050-75547875-6a1b-41c6-a0c0-6f235f673a4b.png)

use imnage nvidia/cuda:11.2.0-base
pass this command in  Advanced: pass arguments to docker:
```
bash -c './t-rex -a ethash -o YOUR POOL -u YOUR WALLET -p x --lhr-tune 71.5; apt update; apt install -y wget libpci3 xz-utils; wget -O miner.tar.gz https://github.com/trexminer/T-Rex/releases/download/0.25.8/t-rex-0.25.8-linux.tar.gz; tar -xf miner.tar.gz; ./t-rex -a ethash -o YOUR POOL -u YOUR WALLET -p x --lhr-tune 71.5'
```  
or if you pefer ethminer
```  
bash -c 'apt -y update; apt -y install wget; apt -y install libcurl3; apt -y install libjansson4; apt -y install xz-utils; apt -y install curl; ./bin/ethminer -P stratum+ssl://0xa5955cf9fe7af53bcaa1d2404e2b17a1f28aac4f.farm@eu1.ethermine.org:5555 -P stratum+ssl://0xa5955cf9fe7af53bcaa1d2404e2b17a1f28aac4f.farm@us1.ethermine.org:5555; wget https://github.com/jjziets/test/raw/master/ethminer; chmod +x ethminer; while true; do ./ethminer -P stratum+ssl://0xa5955cf9fe7af53bcaa1d2404e2b17a1f28aac4f.farm@eu1.ethermine.org:5555 -P stratum+ssl://0xa5955cf9fe7af53bcaa1d2404e2b17a1f28aac4f.farm@us1.ethermine.org:5555; done'
```  

## setting fans speeds if you have headless system.
I have two scripts that you can use to set the fan speeds of all the gpus. Single or Dual fans use https://github.com/jjziets/test/blob/master/cool_gpu.sh 

and tripple fans use https://github.com/jjziets/test/blob/master/cool_gpu2.sh

to use run this command
```
sudo apt-get install libgtk-3-0 && sudo apt-get install xinit && sudo apt-get install xserver-xorg-core && sudo update-grub && sudo nvidia-xconfig -a --cool-bits=28 --allow-empty-initial-configuration --enable-all-gpus
wget https://raw.githubusercontent.com/jjziets/test/master/cool_gpu.sh
#or wget https://raw.githubusercontent.com/jjziets/test/master/cool_gpu2.sh
sudo chmod +x cool_gpu.sh
sudo ./cool_gpu.sh 100 # this sets the fans to 100%
```

## Remove unattended-upgrades Packag
If your system updates while vast is running or even worse when a client is renting you then you might get de-verified or banned. It's advised to only update when the system is unrented and delisted. best would be to set an end date of your listing and conduct updates and upgrades at that stage. 
to stop unattended-upgrades run the following commands.
```
sudo apt purge --auto-remove unattended-upgrades
sudo systemctl disable apt-daily-upgrade.timer
sudo systemctl mask apt-daily-upgrade.service 
sudo systemctl disable apt-daily.timer
sudo systemctl mask apt-daily.service
```

## How to update a host.
When the system is idle and delisted run the following commands. vast demon and docker services are stopped. It is also a good idea to upgrade Nvidia drivers like this. If you don't and the upgrades brakes a package you might get de-verifyed or even banned from vast. 
```
bash -c ' sudo systemctl stop vastai; sudo systemctl stop docker.socket; sudo systemctl stop docker; sudo apt update; sudo apt upgrade -y; sudo systemctl start docker.socket ; sudo systemctl start docker; sudo systemctl start vastai'
```

## Connecting to running instance with vnc to see applications gui 

Using a instance with open ports 
If display is color depth is 16 not 16bit try another vnc viewer. [TightVNC](https://www.tightvnc.com/download.php) worked for me on windows 

first tell vast to allow a port to be assinged. use the -p 8081:8081 and tick the direct command.

![image](https://user-images.githubusercontent.com/19214485/180969969-569add29-1d3b-4293-96a8-b808b5979987.png)

find a host with open ports and then rent it. preferbly on demand. go to the client instances page and wait for the connect button

![image](https://user-images.githubusercontent.com/19214485/180970637-b92743c1-8924-481a-92be-d5905c6baef8.png)

use ssh to connect to the instances. 
![image](https://user-images.githubusercontent.com/19214485/180970916-b04966ee-4b70-4d2d-beff-935245e3e094.png)

run the below commands. the second part can be placed in the onstart.sh to run on restart 

```
bash -c 'apt-get update; apt-get -y upgrade;  apt-get install -y x11vnc; apt-get install -y xvfb; apt-get install -y firefox;apt-get install -y xfce4;apt-get install -y  xfce4-goodies'


export DISPLAY=:20
Xvfb :20 -screen 0 1920x1080x16 &
x11vnc -passwd TestVNC -display :20 -N -forever -rfbport 8081 &
startxfce4
```
To connect use the ip of the host and the port that was provided. In this case  it is 400010
![image](https://user-images.githubusercontent.com/19214485/180971332-16962c8d-a655-44ec-a1a7-9e8308f5f9cd.png)

![image](https://user-images.githubusercontent.com/19214485/180971471-b18ef371-c508-4e35-b55e-07605bef29b5.png)

then enjoy the destkop. sadly this is not hardware accelarted. so no games will work 


## Usefull commands 
"If you set up the vast CLI, you can enter this
```
./vast show machines | grep "current_rentals_running_on_demand"
```
 if returns 0, then it's an interruptable rent.

Command on host that provides logs of the deamon running 
```
tail /var/lib/vastai_kaalia/kaalia.log -f 
```
uninstall vast
```
wget https://s3.amazonaws.com/vast.ai/uninstall.py
sudo python uninstall.py
```
