# Welcome to the 5G-MAG reference tools project<a name="to-the-top"></a>

The 5G-MAG reference tools are a platform to

* **receive 5G broadcast** (5G BC) in dedicated mode, i.e. using 100 % downlink capacity (also known as EnTV/FeMBMS in 3GPP Rel.14 and LTE-based 5G Terrestrial Broadcast in 3GPP Rel.16)
* **combine** 5G BC **with** common (5G) (mobile) **broadband**, process and **cache** it for applications
* **develop interactive applications** and proof-of-concepts for hybrid distribution **for broadcasters**

More general information about 5G Broadcast can be found on the homepage of <a href="https://www.ors.at/en/5g-broadcast/" target="_blank">Austrian Broadcasting Services</a>.

The 5G-MAG reference tools consist of five different repositories:
* [MBMS Modem](mbms-modem) - [Repository](https://github.com/5G-MAG/rt-mbms-modem) 
* [MBMS Middleware](mbms-middleware) - [Repository](https://github.com/5G-MAG/rt-mbms-mw)
* [Webinterface](Webinterface) - [Repository](https://github.com/5G-MAG/rt-wui)
* [Application Process](Application-Process)

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/concept.png">


In case you have any **questions**, need **support** during installation/running OBECA or simply want to **contribute** to the project, please contact **[reference-tools@5g-mag.com](mailto:reference-tools@5g-mag.com)**.

***
## Quickstart
1. Check the [hardware & SDR requirements](Hardware-Requirements) (see our [reference setups](Hardware-Requirements#reference-setups))
2. [Install](mbms-modem#installation) the *MBMS Modem*
3. [Install](mbms-middleware#installation) the *MBMS Middleware* (in case your payload contains HLS or DASH)
3. Optional: 
   * [Install](Webinterface#installation) the *Webinterface* 
4. [Run](mbms-modem#run-the-mbms-modem) the *MBMS Modem* - optional: [*MBMS Middleware*](mbms-middleware#run-the-mbms-middleware), [*Webinterface*](Webinterface#run-the-webinterface)

