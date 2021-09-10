## Table of content
* <a href="#description">Description</a>
* <a href="#how-it-works">How it works</a>
* <a href="#Installation">Installation</a>
* <a href="#run-the-receive-process">Run the *Receive Process*</a>
* <a href="#capture-and-running-of-sample-files">Capture and running of sample files</a>
* <a href="#measurement-recording-and-gps">Measurement recording (and GPS)</a>
* <a href="#logfiles">Logfiles</a>
* <a href="#configuration"> Configuration </a>
  * <a href="#config-file">Config file</a> (general description and *Receive Process* parts)
  * <a href="#restapi">RestAPI</a> (including http(s) and API key config)

## Description
The *Receive Process* builds the lower part of OBECA. Its main task is to convert a 5G BC input signal (received as I/Q raw data from the SDR) to Multicast IP packets on the output. The *Receive Process* can run as background process or can be started/stopped manually. Configuration can be done in the config file or via RestAPI. 

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/concept-rp.png">

In case you have any **questions**, need **support** during installation/running OBECA or simply want to **contribute** to the project, please contact **[obeca@ors.at](mailto:obeca@ors.at)**.

***
## How it works
The main components of the *Receive Process* are implemented as modules for a better overview and to easier improve parts later: 
* Reception of I/Q data from the Lime SDR Mini, for test purposes the real data can be replaced with data from a previously recorded sample file 
* PHY: synchronization, OFDM demodulation, channel estimation, decoding of the physical control and user data channels 
* MAC: evaluation of DCI , CFI, SIB and MIB. Decoding of MCCH and MTCH 
* Read out settings from the configuration file
* RLC / GW: Receipt of MTCH data, output on tun network interface 
* Rest API Server: provides an HTTP server for the RESTful API 
* Logging of status messages via syslog

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/modules-rp.png">

### OBECA uses srsLTE as library
The *Receive Process* is implemented as a standalone C++ application which uses some parts of the [srsLTE](https://github.com/srsLTE/srsLTE) library. In order to use FeMBMS, functional extensions and adjustments in srsLTE are necessary:
* phy/ch_estimation/: Implementation of channel estimation and reference signal for subcarrier spacings 1.25 and 7.5 kHz
* phy/dft/: FFT for subcarrier spacings 1.25 and 7.5 kHz
* phy/phch/: MIB1-MBMS extension
* phy/phch/: support for subcarrier spacings 1.25 and 7.5 kHz
* phy/sch/: BER-calculation added
* phy/ue/: Dynamic selection of sample rate / number of PRB to support sample files and FeMBMS-Radioframestructure (1 + 39)
* asn1: Support for subcarrier_spacing_mbms_r14

### Further open source software
A list of other used open source software components can be found [here](https://github.com/5G-MAG/obeca-receive-process/blob/main/ATTRIBUTION_NOTICE).

***

## Installation

Installation of OBECA consists of 4 simple steps:
1. Install dependencies
2. Install SDR drivers
3. Building the Receive Process
4. Post installation configuration

Please see [the README file in the receive process repository](https://github.com/5G-MAG/obeca-receive-process#readme) for the installation guide.

***

## Run the *Receive Process*
The configuration for the *Receive Process* (center frequency, gain, ports for api, ...) can be changed in the <a href="#config-file">configuration file</a>.
###  Multicast Routing

The *rp* application outputs all received packets on a tunnel (*tun*) network interface. The kernel can be configured to route multicast packets arriving on this internal interface to a network interface, so they are streamed into the local network.

By default, the tunnel interface is named __rp_tun__, and m'cast routing is configured to forward all packets to the default NUC ethernet interface __eno1__.

This can be customized by editing the corresponding environment variables in ``/etc/default/obeca``:
````
### The tun interface to be created for the receive process
RP_TUN_INTERFACE="rp_tun"

### Automatically set up multicast packet routing from the tun interface to a network interface
ENABLE_MCAST_ROUTING=true
MCAST_ROUTE_TARGET="eno1"
````

For changes to take effect, *Receive Process* needs to be restarted: `` sudo systemctl restart rp ``

### Background Process
The RP runs manually or as a background process (daemon).
If the process terminates due to an error, it is automatically restarted. With systemd, execution, automatic start and manual restart of the process can be configured or triggered (systemctl enable / disable / start / stop / restart). 
Starting, stopping and configuring autostart for *rp*: The standard systemd mechanisms are used to control *rp*.

| Command| Result |
| ------------- |-------------|
|  `` systemctl start rp `` | Manually start the process |
|  `` systemctl stop rp `` | Manually stop the process |
|  `` systemctl status rp `` | Show process status |
|  `` systemctl disable rp `` | Disable autostart, rp will not be started after reboot |
|  `` systemctl enable rp `` | Enable autostart, rp will be started automatically after reboot |

#### Troubleshooting: Insufficient permissions when trying to open SDR
*Receive Process* daemon will run under the user ofr (the user created in the [post installation configuration](https://github.com/5G-MAG/obeca-receive-process#step-4-post-installation-configuration)). If this user doesn't have enough permissions to open a SDR through the USB port, you might get the following error when starting *rp* in the background:
````
obeca@NUC:~$ sudo systemctl status rp

rp[10368]: OBECA rp v1.1.0 starting up
< ... >
[WARNING @ host/libraries/libbladeRF/src/backend/usb/libusb.c:529] Found a bladeRF via VID/PID, but could not open it due to insufficient permissions.
[ERROR] bladerf_open_with_devinfo() returned -7 - No device(s) available
< ... >
Process: 10240 ExecStart=/usr/bin/rp (code=dumped, signal=ABRT)
````

To solve this issue simply change the user and group in the corresponding systemd service file (``sudo vi /lib/systemd/system/rp.service``) 
from ofr
````
< ... >
[Service]
< ... >
User=ofr
Group=ofr
< ... >
````
to **your** Ubuntu user (which is obeca in this example).
````
< ... >
[Service]
< ... >
User=obeca
Group=obeca
< ... >
````

### Manual start/stop
If autostart is disabled, the process can be started in terminal using `rp` (ideally with superuser privileges, to allow execution at real time scheduling priority). This will start the *rp* with default log level (info). *Receive Process* can be used with the following OPTIONs:

| Option | | Description |
| ------------- |---|-------------|
|  `` -b `` | `` --file-bandwidth=BANDWIDTH `` | If decoding data from a sample file, specify the channel bandwidth of the recorded data in MHz here (e.g. 5) |
|  `` -c `` | `` --config=FILE `` | Configuration file (default: /etc/obeca.conf) |
|  `` -d `` | `` --sdr_devices `` | Prints a list of all available SDR devices |
|  `` -f `` | `` --sample-file=FILE `` | Sample file in 4 byte float interleaved format to read I/Q data from. <br />If present, the data from this file will be decoded instead of live SDR data.<br /> The channel bandwidth must be specified with the --file-bandwidth flag, and<br /> the sample rate of the file must be suitable for this bandwidth. |
|  ``  -l `` | `` --log-level=LEVEL  `` | Log verbosity: 0 = trace, 1 = debug, 2 = info, 3 = warn, 4 = error, 5 = critical, 6 = none. Default: 2. |
|  `` -p `` | `` --override_nof_prb `` | Override the number of PRB received in the MIB |
|  `` -s `` | `` --srslte-log-level=LEVEL `` |  Log verbosity for srslte: 0 = debug, 1 = info, 2 = warn, 3 = error, 4 = none, Default: 4. |
|  `` -w `` | `` --write-sample-file=FILE `` | Create a sample file in 4 byte float interleaved format containing the raw received I/Q data.|
|  `` -? `` | `` --help `` | Give this help list |
|  `` -V `` | `` --version `` | Print program version |

### Example screenshot
Click [here](https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/v1.1.0_Console_rp.PNG) for an example on what the console output should look like when running the *Receive Process* manually.

***

<a name="samplefiles"></a>
## Capture and running of sample files

Before capturing or running a sample file, make sure that *Receive Process* isn't running in background. If it is, stop *Receive Process* with ``systemctl stop rp``.

### Capture a sample file

In order to capture sample files, you need to have a reception of a 5G BC signal.

Run the command ``rp -w "PathToSample/samplefile.raw"`` to capture the raw I/Q data from the SDR.

### Run a sample file

If you like to start *Receive Process* with a downloaded sample file (see [sample files](sample-files)), you can run the following command:

``rp -f "PathToSample/samplefile.raw" -b 10``

> **Notice:** ``-b 10`` represents the used bandwith when the sample file was captured (see <a href="#Manual-startstop">Manual start/stop</a>). So for a 5 MHz bandwidth sample file you need to adjust the command to ``-b 5``

***

## Measurement recording (and GPS)
### Configuring a GPS mouse

*Receive Process* relies on GPSD (https://gpsd.gitlab.io/gpsd/) for GPS data aquisition.

Please follow the setup instruction for gpsd to configure it for your GPS receiver: https://gpsd.gitlab.io/gpsd/installation.html

Usually, this should boil down to:
- ``sudo apt install libgps-dev gpsd``
- Checking which (virtual) serial port your GPS mouse uses, once you plug it in (e.g. ``/dev/ttyACM0``)
- Setting this device in /etc/default/gpsd:
````
# Devices gpsd should collect to at boot time.
# They need to be read/writeable, either by user gpsd or the group dialout.
DEVICES="/dev/ttyACM0"
# Other options you want to pass to gpsd
GPSD_OPTIONS=""
````
- Adding gpsd to the *dialout* group: `` sudo usermod -a -G dialout gpsd``
- Checking if everything works with one of the client applications, e.g. `cgps` (can be installed with `sudo apt install gpsd-clients`). This should show position data.

### Logging measurement data to a CSV file

#### Configuration for measurement file

Is in ``/etc/obeca.conf``:

```` 
measurement_file:
{
  enabled: true; # if false, measurments are not captured in file
  file_path: "/tmp/rp_measurements.csv";
  interval_secs: 5;
  gpsd:
  {
    enabled: true; # if false, logging without GPS timestamp, latitude and longitude
    host: "localhost";
    port: "2947";
  }
}
````

You can modify the location of the created file here, set the interval in which measurement lines are written to it, and enable/disable GPS.

#### File format

The created file is in semicolo-separated CSV format.

The columns contain:

1. system timestamp
2. latitude
3. longitude
4. gps timestamp
5. CINR
6. PDSCH MCS
7. PDSCH BLER
8. PDSCH BER
9. MCCH MCS
10. MCCH BLER
11. MCCH BER
12. First MCH index
13. First MCH MCS
14. First MCH BLER
15. First MCH BER

If there are more MCHs, they are appended at the end of the line:

'16. Second MCH Index

'17. Second MCH MCS

'18. Second MCH BLER

'19. Second MCH BER

....

#### Example output

````
2021-02-26T15:09:54;48.392428;16.104939;2021-02-26T15:09:54;22.847839;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:00;48.392428;16.104939;2021-02-26T15:10:00;27.173386;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:05;48.392428;16.104939;2021-02-26T15:10:05;26.828796;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:11;48.392428;16.104939;2021-02-26T15:10:11;23.722340;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:17;48.392428;16.104939;2021-02-26T15:10:17;24.914352;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:22;48.392428;16.104939;2021-02-26T15:10:22;26.893414;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:28;48.392428;16.104939;2021-02-26T15:10:28;22.102150;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
2021-02-26T15:10:34;48.392428;16.104939;2021-02-26T15:10:34;22.894867;4;0.000000;0.000000;2;0.000000;-;0;9;0.000000;-;
````

***

## Logfiles

System and *Receive Process* information are logged in the ``/var/log/syslog`` file. 

The log entries in the syslog file are based on the configured log level (see chapter <a href="#Manual-startstop">Manual start/stop</a>). If *rp* is running in background, the used log level will be 2 (info) by default. You can change the used log level by starting *rp* manually and add the parameter ``-l [logNumber]`` (further details on log level can also be found in chapter <a href="#Manual-startstop">Manual start/stop</a>).

For a better overview you can open the syslog file with a filter to only see logging from *Receive Process*:

``cat /var/log/syslog | grep "rp"``

***

## Configuration
### Config file

The config file for *Receive Process* is located in ``/etc/obeca.conf``. The file contains configuration parameters for:
* SDR
* Physical (thread settings)
* RestAPI (see chapter <a href="#RestAPI">RestAPI</a>)
* Measurment file (see chapter <a href="#Measurement-recording-and-GPS">Measurement recording (and GPS)</a>)

````
sdr:
{
  center_frequency_hz = 667000000;
  filter_bandwidth_hz =  10000000;
  search_sample_rate =    7680000;

  normalized_gain = 40.0; # Antenna gain in dB, range depends on used SDR
  antenna = "RX";
  device_args = "driver=bladerf";

  ringbuffer_size_ms = 200;
  reader_thread_priority_rt = 50; # I/Q data reception from the SDR runs in a separate thread, which should have highest priority 
}

phy:
{
  threads = 4;
  thread_priority_rt = 10;  # priority of the frame processing threads
  main_thread_priority_rt = 20;  # priority of the main run loop
}

restful_api:
{
  uri: "https://0.0.0.0:3010/rp-api/";
  cert: "/usr/share/obeca/cert.pem";
  key: "/usr/share/obeca/key.pem";
  api_key:
  {
    enabled: false;
    key: "106cd60-76c8-4c37-944c-df21aa690c1e";
  }
}

measurement_file:
{
  enabled: true;
  file_path: "/tmp/rp_measurements.csv";
  interval_secs: 5;
  gpsd:
  {
    enabled: true;
    host: "localhost";
    port: "2947";
  }
}
````
### RestAPI
RestAPI is supported to show and change configuration of the *Receive Process*. Also the [OBECA.GUI](GUI) process is accessing the API to collect display information.

The API is only accessible when *Receive Process* is running. 
#### API commands

See <a href="https://austrian-broadcasting-services.github.io/obeca-receive-process/api/" target="_blank">API documentation</a> for *Receive Process*. 

####  Securing the RESTful API interface

By default, the startup scripts for *rp* create a self-signed SSL certificate for the RESTful API in ``/usr/share/obeca``, so it can be accessed through https. When calling the API through a webbrowser, you may get a security warning (because of the self-signed certificate), the data stream however, is encrypted.

When using the self-signed certificate, you can for example access the API with the command

``wget --no-check-certificate  -cq https://127.0.0.1:3010/rp-api/status -O -``.

#### Changing the bound interface and port

The API listens on port 3010 on all available interface (0.0.0.0) by default. To change this, modify the URL string in the config file and restart *Receive Process*.

E.g., bind to only the loopback interface and listen on port 4455:
````
restful_api:
{
  uri: "http://127.0.0.1:4455/rp-api/";
 <....>
}
````

**Attention:** without a corresponding change in ``/usr/share/obeca/obeca-gui.py``, this will break the GUI, which also relies on the REST API.

#### Switching to http (no SSL)

To disable the SSL handshake, change the URL string in the config file to "http://" and restart rp.
````
restful_api:
{
  uri: "http://0.0.0.0:3010/rp-api/";
 <....>
}
````

#### Using custom certificates

You can use a 'real' certificate (e.g. obtained through Let's Encrypt) by adjusting the certificate and key file locations in the config file.

````
restful_api:
{
  <...>
  cert: "/usr/share/obeca/cert.pem";
  key: "/usr/share/obeca/key.pem";
  <...>
}
````

#### Using bearer token authentication (a.k.a API key)

````
restful_api:
{
  <...>
  api_key:
  {
    enabled: true;
    key: "106cd60-76c8-4c37-944c-df21aa690c1e";
  }
}
````

When api_key.enabled is set to *true* in the configuration file, requests are only allowed if they contain a matching bearer token in their Authorization header:

`` Authorization: Bearer 106cd60-76c8-4c37-944c-df21aa690c1e ``

You can test this by using curl (or wget) and setting the appropriate header:

`` curl -X GET --header 'Authorization: Bearer 106cd60-76c8-4c37-944c-df21aa690c1e' http://<IP>:<Port>/rp-api/status `` or

`` wget -q --header='Authorization: Bearer 106cd60-76c8-4c37-944c-df21aa690c1e' http://<IP>:<Port>/rp-api/status -O - ``

