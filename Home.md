# Welcome to the OBECA project<a name="to-the-top"></a>

Open Broadcast Edge Cache Appliance (OBECA) is a platform to

* **receive 5G broadcast** (5G BC) in dedicated mode, i.e. using 100 % downlink capacity (also known as EnTV/FeMBMS in 3GPP Rel.14 and LTE-based 5G Terrestrial Broadcast in 3GPP Rel.16)
* **combine** 5G BC **with** common (5G) (mobile) **broadband**, process and **cache** it for applications
* **develop interactive applications** and proof-of-concepts for hybrid distribution **for broadcasters**

More general information about 5G Broadcast can be found on the homepage of <a href="https://www.ors.at/en/5g-broadcast/" target="_blank">Austrian Broadcasting Services</a>.

OBECA consists of five different repositories:
* **[Receive Process](Receive-Process)** [Repository](https://github.com/5G-MAG/obeca-receive-process) 
* **[Gateway Process](Gateway-Process)** [Repository](https://github.com/5G-MAG/obeca-gateway-process)
* **[GUI](GUI)** [Repository](https://github.com/5G-MAG/obeca-gui)
* **[Webinterface](Webinterface)** [Repository](https://github.com/5G-MAG/obeca-web-interface)

<img src="https://github.com/5G-MAG/Documentation-and-Architecture/blob/main/media/wiki/concept.png">


In case you have any **questions**, need **support** during installation/running OBECA or simply want to **contribute** to the project, please contact **[obeca@ors.at](mailto:obeca@ors.at)**.

***
## Quickstart
1. Check the [hardware & SDR requirements](Hardware-Requirements) (see our [reference setups](Hardware-Requirements#reference-setups))
2. [Install](Receive-Process#installation) the *Receive Process*
3. [Install](Gateway-Process#installation) the *Gateway Process* (in case your payload contains HLS or DASH)
3. Optional: 
   * [Install](GUI#installation) the *GUI* (in case you use a display for OBECA)
   * [Install](Webinterface#installation) the *Webinterface* (in case you connect via local network to OBECA)
4. [Run](Receive-Process#run-the-receive-process) the *Receive Process* - optional: [*Gateway Process*](Gateway-Process#run-the-gateway-process), [*GUI*](GUI#run-the-gui), *Webinterface*

