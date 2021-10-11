## Table of content
* <a href="#Description"> Description</a>
* <a href="#How-it-works"> How it works</a>
* <a href="#Installation"> Installation</a>
* <a href="#run-the-mbms-middleware"> Run</a>
* <a href="#Configuration"> Configuration</a>

## Description
The **MBMS Middleware** presents the heart of the 5G-MAG Reference Tools. Its main task is to provide the best available content to the (internal or external) application at any time. If available, it combines content from (mobile) broadband, WiFi with the 5G BC content from the *MBMS Model* using an advanced decision logic. The content is presented to the applications in form of an intelligent edge cache ready for pick up via http(s).

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/concept-gp.png">

> **Status**: The specific parts of the *MBMS Middleware*, especially outer interfaces and the surrounding environment is currently subject of investigation of the project team.  The project team targets to use as much as possible from existing specifications and concepts (3GPP, DVB,...) for the *MBMS Middleware*.

## How it works
* In the current release 0.9.x the *MBMS Middleware* uses the UDP multicast IP packets from the *MBMS Model*.
* If the payload contains FLUTE decoded content (files, i.e. Service Announcement, DASH, HLS) the *MBMS Middleware* decodes the packets with its FLUTE/ALC decoder into files.
* The *MBMS Middleware* includes a web-cache server and each service is available like an CDN publishing point including manifest and segment files.
* Information how to access the endpoints (e.g. URL to manifest.m3u8 or manifest.mpd) can be found on the corresponding Middleware tab in the *GUI* or *Webinterface*.
> **Note:** The FLUTE/ALC decoder is available as an independent repository (library) [here](https://github.com/5G-MAG/libflute) and includes encoding/decoding functionalities.

## Installation
Installation of the *MBMS Middleware* consists of 2 steps:
1. Install dependencies
2. Building the *MBMS Middleware*

Please see [the README file in the MBMS Middleware repository](https://github.com/5G-MAG/rt-mbms-mw#readme) for the installation guide.

## Run the MBMS Middleware
The configuration for the *MBMS Middleware* (file paths, max file age, api ports, ...) can be changed in the <a href="#config-file">configuration file</a>.
When starting, the *MBMS Middleware* listens to the local [tun interface](https://github.com/5G-MAG/Documentation-and-Architecture/wiki/MBMS-Modem#multicast-routing). Received multicast packets from the *Receive Process* are FLUTE decoded and the files are stored in the cache.

### Background process
The *MBMS Middleware* runs manually or as a background process (daemon).
If the process terminates due to an error, it is automatically restarted. With systemd, execution, automatic start and manual restart of the process can be configured or triggered (systemctl enable / disable / start / stop / restart).
Starting, stopping and configuring autostart for *5gmag-rt-mw*: The standard systemd mechanisms are used to control *5gmag-rt-mw*:

| Command| Result |
| ------------- |-------------|
|  `` systemctl start 5gmag-rt-mw `` | Manually start the process in background |
|  `` systemctl stop 5gmag-rt-mw `` | Manually stop the background process |
|  `` systemctl status 5gmag-rt-mw `` | Show process status |
|  `` systemctl disable 5gmag-rt-mw `` | Disable autostart, 5gmag-rt-mw will not be started after reboot |
|  `` systemctl enable 5gmag-rt-mw `` | Enable autostart, 5gmag-rt-mw will be started automatically after reboot |

### Manual start/stop
After [installing](https://github.com/5G-MAG/rt-mbms-mw#readme) the *MBMS Middleware* you can start the process manually in the terminal using the command ``mw``. This will start the *mw* with default log level (info). *MBMS Middleware* can be used with the following OPTIONs:

| Option | | Description |
| ------------- |---|-------------|
|  `` -c `` | `` --config=FILE `` | Configuration file (default: /etc/5gmag-rt.conf)) |
|  `` -i `` | `` --interface=IF `` | IP address of the interface to bind flute receivers to (default: 192.168.180.10) |
|  `` -l `` | `` --log-level=LEVEL  `` | Log verbosity: 0 = trace, 1 = debug, 2 = info, 3 = warn, 4 = error, 5 = critical, 6 = none. Default: 2. |
|  `` -? `` | `` --help `` | Give this help list |
|  `` -V `` | `` --version `` | Print program version |

## Configuration
### Config file

The config file for *MBMS Middleware* is located in ``/etc/5gmag-rt.conf)``. The file contains configuration parameters for:
* Cache
* HTTP-Server

````
mw: 
{
  cache: { 
    max_file_age: 30;    /* seconds */
    max_total_size: 512; /* megabyte */
  }
  http_server: {
    uri: "http://0.0.0.0:3020/";
    api_path: "mw-api";
    cert: "/usr/share/5gmag-rt/cert.pem";
    key: "/usr/share/5gmag-rt/key.pem";
    api_key:
    {
      enabled: false;
      key: "106cd60-76c8-4c37-944c-df21aa690c1e";
    }
  }
}

````