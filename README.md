Fazer um logo

# Introduction

Clouds-1000 is a dataset of 1000 sky images captured with cameras directed towards the horizon in the north and south directions in an area with a good view of the sky in the UFSC Photovoltaic Laboratory at the Federal University of Santa Catarina, in the city of Florianópolis/SC-Brazil. The images were collected every minute over the period of March–October of 2021.

# Reproduction
In order to reproduce our dataset you'll need to first configure the OS for capturing the images.

## Nimbus Gazer
### Summary
The Nimbus Gazer is an adaptation of the [motionEyeOS](https://github.com/motioneye-project/motioneyeos/wiki), which is a Linux distribution that turns a single-board computer into a video surveillance system. The motionEyeOS OS is based on [BuildRoot](http://buildroot.uclibc.org/) and uses [Motion](https://motion-project.github.io/) as a backend and [motionEye](https://github.com/ccrisan/motioneye/) for the frontend.

Nimbus Gazer uses motionEye version 0.41 and Motion version 4.2.2. The system is set to GMT location zone and prevents any camera LEDs from blinking, by disabling all LEDs in the boot configuration file. This configuration is set by default to prevent any LED reflection onto the camera lens. The system is set to start capturing images at 08:00 GMT and stop at 22:00 GMT. The chosen time interval was defined to capture only images with at least some level of sunlight. The time zone of our research lab is at GMT-3. To install the OS, it is necessary to have at least 32GB of free memory.

Our configuration of the motion system is set at the lowest available frame rate of 1 frame per minute to match the time resolution of sensory data from our lab. That means that every minute, an image is captured.

Captured images are configured at 2592 x 1944 resolution and are stored in a local directory before being uploaded to the cloud (the default directory is `/Nuvens/camtest/`). We use the built-in option to upload to a Google Drive directory to upload the images.

For monitoring, the system sends a health-check e-mail each time the system either: boots-up, start or stop recording.

### Step-by-step Configuration
#### Nimbus Gazer Installation
An operating system image with (almost) everything configured available at [this google drive url](https://drive.google.com/file/d/1BA6pl_VN2R-rHviC5AHUT_1Ph2xF2bjp/view?usp=sharing). The image file is called `caming.img` and it requires a micro SD card of at least 32 GB to be installed. The only thing you will have to configure are:
1.  Passwords (comes with pi username and pi password for the OS and admin and no password for the interface. We suggest using the default North and South cameras described below.
2.  Time zone: comes with the São Paulo time zone.
3.  Set the name of your camera (hostname comes pre-named from camtest ). Use raspi-config for this.
4.  Define in the web interface the name of the folder where the images will be saved. It comes pre-configured to `/Clouds/camtest` and has to be changed. Always use `/Clouds/<camera-name>`.

To record to a new SD card and create a new Nimbus Gazer you can follow [these](https://www.tomshardware.com/how-to/back-up-raspberry-pi-as-disk-image) instructions. Alternatively you can download a Raspberry Pi Imager from [here](https://www.raspberrypi.org/downloads/) and follow the instructions in the figures below:
1. ![image](https://user-images.githubusercontent.com/9988985/174694080-2609b9bb-7daa-4ae6-827f-a615696a282b.png)
2. ![image](https://user-images.githubusercontent.com/9988985/174694089-47cebd1c-1ab9-4a9e-94af-b06599dafc2a.png)
3. ![image](https://user-images.githubusercontent.com/9988985/174694096-af76aba0-9358-48ab-a3bb-8d3b541c3cda.png)
4. ![image](https://user-images.githubusercontent.com/9988985/174694115-9f540965-a3ff-4b0e-a60c-f0dfef244d71.png)

#### Linux Settings
Set the time zone to GMT via `raspi-config`:

    sudo raspi-config

![image](https://user-images.githubusercontent.com/9988985/174693896-b647ffd8-dfc2-44f6-aa6d-9d960014a88a.png)
![image](https://user-images.githubusercontent.com/9988985/174693905-d1701e82-df17-4dfa-9a6b-dbe29e8700b8.png)

To prevent the any LED from blinking and possibly ruining your images. Edit `/boot/config.txt` and add to the end of the file:

    disable_camera_led=1

The default crontab configuration is set as follows:

    @reboot sleep 60 && /root/boot-email.sh
    # m h  dom mon dow   command
    # obs.: time always on GMT 0
    # Stop motionEye server at 1900 nighttime
    0 22 * ​​* * /root/stop-recording.sh
    # Restart motionEye server at 0500 in the morning
    0 8 * * * /root/start-recording.sh

#### Configuring Nimbus Gazer's Health-check Emails
There are three scripts that automatically send emails, all located in the `/root` folder:
-   `boot-email.sh` - sends an e-mail when the system is booted.
-   `start-recording.sh` - sends an e-mail when the system starts recording.
-   `stop-recording.sh` - sends an e-mail when the system stops recording.

The first is an "I'm alive" script that, as soon as the network is up, sends an email indicating the IP and hostname of the camera. The other two are scripts that stop or restart recording. Each of these scripts has one or more triplets similar to the three below (message content may vary):

    # Email to <username>
    echo "Sending email to <username>..."
    echo -e "`echo "This is your automated Cloud Gazer reporting...\\nHorizon camera:"; hostname; echo "is booting with address: "; sudo ifconfig eth0 | grep 'inet '; echo -e "on:"; date; echo -e "\\nRTC time is: "; sudo hwclock -r; echo -e "\\nDisk space is as follows: "; df -mH /; echo -e "\\nStatus of the MotionEye server is:"; systemctl is-active motioneye; echo -e "\\nHave fun gazing at clouds!"`" | s-nail -v -s "`hostname`: `date '+%Y-%m-%d'`" <email address>

⚠ If you want to change the recipient, just replace the address at the end of the last line. Duplicate this set of lines as much as you like so that all desired recipients receive emails.

⚠ These scripts use Google's Gmail smtp server and the nimbus.gazer@gmail.com account to send emails. If you want to change any of these settings, you will have to reconfigure the s-nail which is the email utility used here. To do so, modify the `/etc/ssmtp/ssmtp.conf` file. The current configuration is in the figure below:

![image](https://user-images.githubusercontent.com/9988985/175119614-9635d23e-cb03-4267-a1ef-ce4c8025050f.png)

#### Camera Access
To access the camera web interface go to `<camera ip>:8765` and input your credentials. For ssh access you can  simply execute: `pi <username>` via terminal.

# Clouds-1000 Dataset
[Clouds-1000](https://data.mendeley.com/datasets/4pw8vfsnpx/1) is a dataset of 1000 sky images captured with cameras directed towards the horizon in the north and south directions in an area with a good view of the sky in the UFSC Photovoltaic Laboratory at the Federal University of Santa Catarina, in the city of Florianópolis/SC-Brazil. The images were collected every minute over the period of March–October of 2021.

To construct the dataset, images were captured with cameras directed towards the horizon in the north and south directions in an area with good view of the sky in the UFSC Photovoltaic Laboratory at the Federal University of Santa Catarina. The images were collected every minute over the period of March - October of 2021. In order to capture images from the sky, our research group developed a low-cost equipment using Raspberry Pi with a custom Operational System (OS) we call Nimbus Gazer. The figure below shows the equipment installed at the Photovoltaic Laboratory, in Florianópolis.

![image](https://user-images.githubusercontent.com/9988985/177363154-aeab6245-f264-4308-8974-34c8f8eda128.png)

At the lab we have two equipment with cameras aiming just above the horizon line in order to capture the sky and incoming clouds. Based on previous analysis, our team decided that the best locations to point the cameras was the North and South. This is mainly due to the most predominant winds in the area, this way we can see clouds approaching the area more clearer. Both equipment are encased in a aluminum shell for protection against the weather, with a clear acrylic at the front where the camera is located.

The cameras we used are the Raspberry Pi camera model V1.3, with a field of view of 65º. Since we have two cameras aiming at opposite directions, we have the equivalent of a 130º view of the sky at all times. The equipment is connected via ethernet cable to the lab facility.

For the task of image annotation, our research team was divided into 3 Data Analysts responsible for analyzing and labeling the images, and 2 meteorologists responsible for supervision and validation. Sylvio Mantelli is a PhD from INPE, working on our research team and helped with data labeling mentoring and several analysis throughout development of the dataset. Maurici Amantino Monteiro, professor and currently climatologist at Aqueris Engenharia e Soluções Ambientais. With several years of experience in synoptic observation, he helped us to understand the nature of clouds and associate them with their corresponding classes.

The annotations were handmade using the [Supervisely](https://supervise.ly/) tool. The tool was created for image annotation and data management in which it's possible to create the annotations via interface available, similar to other image editors. Each image was annotated with the polygon tool and classified using 4 cloud types: Cirriform, Cumuliform, Stratiform, Stratocumuliform and 1 class representing trees and buildings. This classification is based on solar radiation absorption characteristics. Due to the humid climate of the region, the Cumulonimbus (Cb) cloud seldom forms. This type of cloud usually form in dryer regions, thus we won't find any occurrence of this cloud in the dataset.

The cloud type distribution is shown in the table below and a few image examples of north and south images can be seen in the next figure.

| Cloud Type      | Amount in Dataset | % in Dataset |
| ----------- | ----------- | ----------- |
| Arvore      | 989       | 99.30%       |
| Estratocumuliformes   | 812        | 81.53%        |
| Estratiformes      | 271       | 27.21%       |
| Cirriformes   | 285        | 28.61%        |
| Cumuliformes      | 90       | 9.04%       |

![image](https://user-images.githubusercontent.com/9988985/177363896-b2132747-e175-4b1a-b7ea-8d82e31811de.png)



# License
Clouds-1000 is released under the [Creative Commons Attribution 4.0 International license](https://github.com/bjuncklaus/Clouds-1000/blob/main/LICENSE).

# Acknowledgement
Thanks to Marco Polli, Allan Cerentini, Thiago Chaves and [nicolasbranco](https://github.com/nicolasbranco) for helping with data labeling.
  
  Thanks to Juliana Arrais for data labeling tutoring, data labeling and validation.
  
  Thanks to Sylvio Mantelli for data labeling tutoring and validation.
  
  Thanks to Aldo von Wangenheim for creating the Nimbus Gazer system and for data labeling configuration and validation.

# Citation
If you find our dataset useful in your research, please consider citing:

    @misc{martins2022clouds1000,
      title={Clouds-1000},
      author={Juncklaus Martins, Bruno and Polli, Marco and Cerentini, Allan and Mantelli, Sylvio and Chaves, Thiago and Moreira Branco, Nicolas and von Wangenheim, Aldo and Arrais, Juliana},
	  institution={Universidade Federal de Santa Catarina},
      year={2022},
	  month={06},
      organization={Mendeley Data},
	  url={https://data.mendeley.com/datasets/4pw8vfsnpx/1},
	  howpublished = {\url{https://github.com/bjuncklaus/Clouds-1000}},
	  doi = {10.17632/4pw8vfsnpx.1}
    }
