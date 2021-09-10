The **Gateway Process** presents the heart of OBECA. Its main task is to provide the best available content to the (internal or external) application at any time. If available, it combines content from (mobile) broadband, WiFi with the 5G BC content from the *Receive Process* using an advanced decision logic. The content is presented to the applications in form of an intelligent edge cache ready for pick up via http(s).

* <a href="#How-it-works"> How it works</a>
* <a href="#Installation"> Installation</a>
* <a href="#run-the-gateway-process"> Run the *Gateway Process*</a>
* <a href="#Configuration"> Configuration</a>

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/concept-gp.png">

> **Status**: The specific parts of the *Gateway Process*, especially outer interfaces and the surrounding environment is currently subject of investigation of the project team.  The project team targets to use as much as possible from existing specifications and concepts (3GPP, DVB,...) for the *Gateway Process*.  

## How it works
* In the current release 0.9.x the *Gateway Process* uses the UDP multicast IP packets from the *Receive Process*.
* If the payload contains FLUTE decoded content (files, i.e. Service Announcement, DASH, HLS) the *Gateway Process* decodes the packets with its FLUTE/ALC decoder into files.
* The *Gateway Process* includes a web-cache server and each service is available like an CDN publishing point including manifest and segment files.
* Information how to access the endpoints (e.g. URL to manifest.m3u8 or manifest.mpd) can be found on the corresponding Gateway tab in the *GUI* or *Webinterface*.
> **Note:** The FLUTE/ALC decoder is available as an independent repository (library) [here](https://github.com/5G-MAG/libflute) and includes encoding/decoding functionalities.

## Installation
Installation of the *Gateway Process* consists of 2 steps:
1. Install dependencies
2. Building the *Gateway Process*

Please see [the README file in the gateway process repository](https://github.com/5G-MAG/obeca-gateway-process/#readme) for the installation guide.

## Run the Gateway Process
The configuration for the *Gateway Process* (file paths, max file age, api ports, ...) can be changed in the <a href="#config-file">configuration file</a>. 
When starting, the *Gateway Process* listens to the local [tun interface](https://github.com/5G-MAG/Documentation-and-Architecture/wiki/Receive-Process#multicast-routing). Received multicast packets from the *Receive Process* are FLUTE decoded and the files are stored in the cache.

### Background process
The *Gateway Process* runs manually or as a background process (daemon).
If the process terminates due to an error, it is automatically restarted. With systemd, execution, automatic start and manual restart of the process can be configured or triggered (systemctl enable / disable / start / stop / restart). 
Starting, stopping and configuring autostart for *gw*: The standard systemd mechanisms are used to control *gw*:

| Command| Result |
| ------------- |-------------|
|  `` systemctl start gw `` | Manually start the process in background |
|  `` systemctl stop gw `` | Manually stop the background process |
|  `` systemctl status gw `` | Show process status |
|  `` systemctl disable gw `` | Disable autostart, gw will not be started after reboot |
|  `` systemctl enable gw `` | Enable autostart, gw will be started automatically after reboot |

### Manual start/stop
After [installing](https://github.com/5G-MAG/obeca-gateway-process/#readme) the *Gateway Process* you can start the process manually in the terminal using the command ``gw``. This will start the *gw* with default log level (info). *Gateway Process* can be used with the following OPTIONs:

| Option | | Description |
| ------------- |---|-------------|
|  `` -c `` | `` --config=FILE `` | Configuration file (default: /etc/obeca.conf) |
|  `` -i `` | `` --interface=IF `` | IP address of the interface to bind flute receivers to (default: 192.168.180.10) |
|  `` -l `` | `` --log-level=LEVEL  `` | Log verbosity: 0 = trace, 1 = debug, 2 = info, 3 = warn, 4 = error, 5 = critical, 6 = none. Default: 2. |
|  `` -? `` | `` --help `` | Give this help list |
|  `` -V `` | `` --version `` | Print program version |

## Configuration
### Config file

The config file for *Gateway Process* is located in ``/etc/obeca.conf``. The file contains configuration parameters for:
* Cache
* HTTP-Server

````
gw:
{
  cache: {
    base_path: "/tmp/obeca"; # Path where files shall be cached
    max_file_age: 25;    /* seconds */
    max_total_size: 512; /* megabyte */
  }
  http_server: {
    uri: "http://0.0.0.0:3020/";
    api_path: "gw-api";
    cert: "/usr/share/obeca/cert.pem";
    key: "/usr/share/obeca/key.pem";
    api_key:
    {
      enabled: false;
      key: "106cd60-76c8-4c37-944c-df21aa690c1e";
    }
  }
}

````

