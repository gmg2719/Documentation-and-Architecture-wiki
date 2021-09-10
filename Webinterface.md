## Table of content

* <a href="#Description"> Description </a>
* <a href="#How-it-works"> How it works </a>
* <a href="#Installation"> Installation </a>
* <a href="#Run-the-Webinterface"> Run the Webinterface </a>

## Description
The *OBECA Webinterface* provides an optional graphical webinterface with a control display for each OBECA process (
Receive, Gateway, Application). Its main purpose is to show information of each process. It shows the same information
as the [GUI](https://github.com/5G-MAG/Documentation-and-Architecture/wiki/GUI), but other than that, the Webinterface
is accessible via http(s). Thus it works well for remote monitoring any OBECA process.

In case you have any **questions**, need **support** during installation/running OBECA or simply want to **contribute**
to the project, please contact **[obeca@ors.at](mailto:obeca@ors.at)**.
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

Please see [the README file in the Webinterface repository](https://github.com/5G-MAG/obeca-web-interface#readme) for
the installation guide.

***

## Run the Webinterface

### Manual start/stop

You can start the Webinterface manually in the OBECA Webinterface repository
after [installing](https://github.com/5G-MAG/Documentation-and-Architecture/obeca-web-interface#readme) it:

````
cd ~
cd obeca-web-interface
node app.js 
````

You can access the Webinterface through the nginx server on port 80 in your browser: `` http://<ip address> ``. If *
Receive Process* is running, the *OBECA Webinterface* gets its information about the *Receive Process* through the API.
Click [here](https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/Webiface_rp.PNG) for an
example screenshot.

### Background process (**recommended**)

The OBECA Webinterface can also be configured to run in background (as a daemon). Therefore the ExecStart path needs to
be adjusted to the right app.js application.

**Step 1:** Get the right path to your app.js file

Go to your OBECA Webinterface repo:

````
cd ~
cd obeca-web-interface
pwd
````

This will output you the path to your folder. For example:

````
/home/obeca/obeca-web-interface
````

**Step 2:** Change the ExecStart path

Navigate to the ``obeca-web-interface.service`` file:

````
cd ~
cd obeca-web-interface/supporting_files/
````

open the ``obeca-web-interface.service`` file with an editor, it should look like this:

````
[Service]
< ... >
ExecStart=/usr/bin/node /home/ofr/workspace/obeca-web-interface/app.js
< ... >
````

Replace the path to the app.js file with the path from the first step:

````
[Service]
< ... >
ExecStart=/usr/bin/node /home/obeca/obeca-web-interface/app.js
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
