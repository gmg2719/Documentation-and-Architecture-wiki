## Table of content

* <a href="#Description"> Description </a>
* <a href="#How-it-works"> How it works </a>
* <a href="#Installation"> Installation </a>
* <a href="#Run-the-Webinterface"> Run the Webinterface </a>

## Description
The *5G-MAG Reference Tools Webinterface* provides an optional graphical webinterface with a control display for each 5G-MAG Reference Tools process (
MBMS Modem, MBMS Middleware, Application). Its main purpose is to show information of each process. It shows the same information
as the [GUI](https://github.com/5G-MAG/Documentation-and-Architecture/wiki/GUI), but other than that, the Webinterface
is accessible via http(s). Thus it works well for remote monitoring any 5G-MAG Reference Tools process.

In case you have any **questions**, need **support** during installation/running the 5G-MAG Reference Tools or simply want to **contribute**
to the project, please contact **[reference-tools@5g-mag.com](mailto:reference-tools@5g-mag.com)**.
***

## How it works

The Webinterface displays all relevant information for each process. It consists of three tabs: Receive, Gateway and
Application. All data is provided by the RestAPIs of the particular processes.

***

## Installation

Installation of the *Webinterface* consists of 4 steps:

1. Install dependencies
2. Building the *Webinterface*
3. Post installation configuration
4. Run the *Webinterface*

Please see [the README file in the Webinterface repository](https://github.com/5G-MAG/rt-wui/blob/main/README.md) for
the installation guide.

***

## Run the Webinterface

### Manual start/stop

You can start the Webinterface manually in the 5G-MAG Reference Tools Webinterface repository
after [installing](https://github.com/5G-MAG/rt-wui/blob/main/README.md) it:

````
cd ~
cd rt-wui
node app.js 
````

You can access the Webinterface through the nginx server on port 80 in your browser: `` http://<ip address> ``. If *
Receive Process* is running, the *5G-MAG Reference Tools Webinterface* gets its information about the *MBMS Modem* through the API.
Click [here](https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/Webiface_rp.PNG) for an
example screenshot.

### Background process (**recommended**)

The 5G-MAG Reference Tools Webinterface can also be configured to run in background (as a daemon). Therefore the ExecStart path needs to
be adjusted to the right app.js application.

**Step 1:** Get the right path to your app.js file

Go to your 5G-MAG Reference Tools Webinterface repo:

````
cd ~
cd rt-wui
pwd
````

This will output you the path to your folder. For example:

````
/home/rt-wui
````

**Step 2:** Change the ExecStart path

Navigate to the ``obeca-web-interface.service`` file:

````
cd ~
cd rt-wui/supporting_files/
````

open the ``obeca-web-interface.service`` file with an editor, it should look like this:

````
[Service]
< ... >
ExecStart=/usr/bin/node /home/ofr/workspace/rt-wui/app.js
< ... >
````

Replace the path to the app.js file with the path from the first step:

````
[Service]
< ... >
ExecStart=/usr/bin/node /home/rt-wui/obeca-web-interface/app.js
< ... >
````

save and exit.

**Step 3:** Copy to systemd

Copy the file to the systemd folder:

````
sudo cp obeca-web-interface.service /lib/systemd/system/
````

**Step 4:** Relead the daemon

````
sudo systemctl daemon-reload
````

You can now run the Webinterface in the background. The standard systemd mechanisms can be used to control the
webinterface:

| Command | Result |
| ------------- |-------------|
|  `` systemctl start obeca-web-interface.service `` | Manually start the process in background |
|  `` systemctl stop obeca-web-interface.service `` | Manually stop the process in background |
|  `` systemctl status obeca-web-interface.service `` | Show process status |
|  `` systemctl disable obeca-web-interface.service `` | Disable autostart, Webinterface will no be started after reboot |
|  `` systemctl enable obeca-web-interface.service `` | Enable autostart, Webinterface will be started automatically after reboot |
