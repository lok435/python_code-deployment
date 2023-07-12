# AI Accelerator powered by Meghnad CPU

### Copyright
All rights reserved. Inxite Out Pvt Ltd. 2022.

### Author
Saurabh Singh (saurabh.singh@inxiteout.ai)

### Operating System
Ubuntu 22.04

# Pre-Requistes
Below Packages need to be installed:
- [python](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-22-04-server)
- [ngnix](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-22-04) 
- [anconda](https://www.digitalocean.com/community/tutorials/how-to-install-the-anaconda-python-distribution-on-ubuntu-22-04)

### Clone repository
```sh
git clone https://gitlab.com/ixo_admin/ixolerator.git
cd ixolerator
```
### Create directories
```sh
mkdir ~/s3_bucket
mkdir ~/adl
sudo mkdir /mnt/resource/blobfusetmp -p
```

### Setup Instructions
```sh
conda create -n vti_app python=3.9.12 anaconda
source activate vti_app
conda install pytorch=1.11.0 torchvision==0.12.0 torchaudio==0.11.0 cpuonly -c pytorch
```

### Install Pre-required Softwares.
```sh
sudo apt-get update && sudo apt install -y make && sudo apt install ca-certificates && sudo apt install s3fs -y && sudo apt install -y libx11-dev &&
sudo apt-get install -y build-essential && sudo apt-get install -y libsndfile1 && sudo apt-get install -y libsndfile1-dev && 
sudo apt-get install -y python3-enchant && 
sudo apt-get install -y wget && sudo apt-get install -y git && sudo apt-get clean
```

### Install Pre-required Python packages and dependencies.

```sh
python -m pip install --upgrade pip
pip install -e .
pip install -r apps/requirements.txt
```

## Meghnad
```sh
To Setup Meghnad project follow this steps mentioned under the subsections "Package", "Apps", "Speech" (Meghnad); <module_name> - zero_shot_stt,
"Connectors (AWS or Azure)" and "Text To Insights" (Solutions) under "Setup Instructions"
```
#### NLP modules
```sh
pip install -r meghnad/interfaces/nlp/<module_name>/requirements.txt
python -m coreferee install en
```

#### CV modules
```sh
pip install -r meghnad/interfaces/cv/<module_name>/requirements.txt
```

#### STD ML modules
```sh
pip install -r meghnad/interfaces/std_ml/<module_name>/requirements.txt
```

#### TS modules
```sh
pip install -r meghnad/interfaces/ts/<module_name>/requirements.txt
```

#### Speech modules
```sh
#sudo apt-get install ffmpeg

pip install -r meghnad/interfaces/speech/<module_name>/requirements.txt
```

### Connectors

#### AWS
```sh
chmod a+x connectors/aws/script.sh
bash connectors/aws/script.sh
pip install -r connectors/aws/requirements.txt
```

#### Azure
```sh
chmod a+x connectors/azure/script.sh
bash connectors/azure/script.sh
pip install -r connectors/azure/requirements.txt
```
### Solutions

#### Interview Mining
```sh
pip install -r solutions/interfaces/interview_mining/requirements.txt
```
#### Aspect Based Perception Detection
```sh
pip install -r solutions/interfaces/aspect_based_perception_detection/requirements.txt
```

#### Opinion Mining
```sh
pip install -r solutions/interfaces/opinion_mining/requirements.txt
```

#### Text To Insights
```sh
pip install -r solutions/interfaces/text_to_insights/requirements.txt
```

#### Group Discussion Summary
```sh
pip install -r solutions/interfaces/group_discussion_summary/requirements.txt
```

### Copy sercet file
```sh
.passwd-s3fs & fuse_connection.cfg
```

### Replace the Values in config.pf file.
replace below values in "vi ~/ixolerator/connectors/aws/s3/config.py"

```
'password_file_path': '/home/ubuntu/.passwd-s3fs',
'uid':'1000',
'gid':'1000'
```

### Create a code for Application start
- Create vti_start.sh file with Vi or any editor and paste below code.

```sh
#!/bin/bash
eval "$(conda shell.bash hook)"
conda activate vti_app
python -u connectors/aws/src/s3_mount_linux.py
python -u apps/demo_app_solutions_vti/backend/server/src/vti_server.py init_mode='tti' access_type='info' track_usage=1 dump_logs=1 logfile_dir="/home/ubuntu/logs"
```

- Provide execute permissions for vti_start.sh file.
```sh
chmod +x vti_start.sh
```

- Create vti.service file with Vi or any editor in "/etc/systemd/system/vti.service" path and paste below code in it.
```sh
[Unit]
Description=VTI start

[Service]
Environment="PATH=/home/ubuntu/anaconda3/bin:/home/ubuntu/anaconda3/condabin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
WorkingDirectory=/home/ubuntu/ixolerator
Restart=always
User=ubuntu
Group=ubuntu
ExecStart=/home/ubuntu/ixolerator/vti_start.sh

[Install]
WantedBy=multi-user.target
```

### Start and Enable VTI Service

```sh
sudo systemctl enable vti
sudo systemctl start vti
```

