# Release Plan

## August 2021

### OBECA.rp

* Rel16: PBCH Repetition, Aggregation Level 16, CFI-Indicator in MIB-MBMS​

### OBECA.gp

* Payloads: DASH native

<a name="backlog"></a>

# Backlog

* DRM​, Targeted Ads​ support...
* Decision logic, advanced cache support, CMAF support
* ROUTE support, DVB-I, MBMS-API, SCS 0.37 kHz and 15 kHz
* Service Discovery
* Additional SDR: [Dektec DTA-2131](https://www.dektec.com/products/PCIe/DTA-2131/) SDR PCIe card
* ...

<a name="releases"></a>

# Release Notes

## 28.07.2021

### OBECA.gw v0.9.0

* Receive multicast packets
* Basic Flute/ALC Decoder
* HLS support
* caching HLS files on nginx

### OBECA.Webinterface v1.0.0

* Receive Process - SDR parameter: frequency, gain, sample rate, filter BW, buffer level, spectrum
* Receive Process - System parameter: CPU, Mem, Network, CPU temp
* Receive Process - Constellation diagrams, BLER, BER, MCS: PDSCH, PMCH/MCCH, PMCH/MTCH
* Receive Process - Sync parameter: Sync status, CFO, Cell ID, PRM, Bandwidth, SCS (subcarrier-spacing)
* Receive Process - Service information: MCH, MCS, TMGI, Multicast IP and Port
* Gateway Process - Service Information​: RTP, DASH, HLS Information​
* Application Process - Click ‘n play: Website with HLS.js/DASH.js player

***

## 06.07.2021

### OBECA.rp v1.1.0

* Extended SDR support via [SoapySDR API](https://github.com/pothosware/SoapySDR/wiki)
* Extended API
* Sample file recording improved
* Faster build (disabled building of unused srsLTE applications)
* Minor fixes

### OBECA.obeca-gui v1.1.0

* Receive Process - Adaptions for SoapySDR (gain in dB, rp API changes)
* Receive Process - Display gain range
* Receive Process - Display CINR
* Receive Process - Disabled verification of SSL certs, so self-signed certificates will work in https connections
* Receive Process - Minor wording fixes
* Gateway Process - Service Information​: RTP, DASH, HLS Information​
* Application Process - Click ‘n play: player for RTP Payload​, website with HLS.js/DASH.js player

***

## 29.3.2021

### OBECA.rp v1.0

* Payloads:​ RTP native​, HLS (via 3rd party middleware)​
* Input: I/Q Data via LimeSDR​ Mini
* Output: IP packets on tun-interface​
* Channel Bandwidths: 3, 5, 6, 7, 8, 10 MHz​
* Modulation Coding Scheme 0-26 (T 7.1.7.1.-1 TS 36.213) ​
* Sub Carrier Spacing: 1.25 kHz, 2.5 kHz, 7.5 kHz​
* Cyclic Prefix: 33.3 us, 200 us​
* Multiple MCHs, MTCHs​
* Play/Record Sample Files (raw data)​
* Recording of signal parameteres (CINR, BER, BLER…)​
* Config File, Log File, Data Recoding (w/ GPS)​
* Run as background process or manual start/stop​
* SDR support: LimeSDR Mini​

### OBECA.obeca-gui v1.0

* Receive Process - SDR parameter: frequency, gain, sample rate, filter BW, buffer level, spectrum
* Receive Process - System parameter: CPU, Mem, Network, CPU temp
* Receive Process - Constellation diagrams, BLER, BER, MCS: PDSCH, PMCH/MCCH, PMCH/MTCH
* Receive Process - Sync parameter: Sync status, CFO, Cell ID, PRM, Bandwidth, SCS (subcarrier-spacing)
* Receive Process - Service information: MCH, MCS, TMGI, Multicast IP and Port

***
