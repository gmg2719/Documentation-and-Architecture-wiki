
## Next meeting
The next meeting will take place on October 8th 2021

### Dial-in data
Join Zoom Meeting
https://us06web.zoom.us/j/81143890686?pwd=VzRxTkpxMHlWRVJrYXUxblV6c1BlQT09

## Minutes 08.10.2021

### Participants
Jordi J. Gimenez (5G-MAG) (5G Media Action Group), Alvaro (UPV), Daniel Silhavy, David Fernández Piñas, Ben Smith (EDT), UMESH TALLAM, Stefan Babel, Martin Holovlasky (KRONEHIT), Klaus Kuehnhammer, Christoph Tanzer (ORS) (Christoph Tanzer), Romain Bouqueau, Gerjo Bruntink (NPO), Louay Bassbouss (Fraunhofer FOKUS), [Eurofins] Ian Medland, Kurt Krauss (Dolby), Tünde Kaufman, Pierre Brétillon, Richard Bradbury (BBC), Thomas Stockhammer, Christophe Burdinat (ATEME), Gwendal Simon (Synamedia) (Gwendal Simon), cthienot, Federico Maria Pandolfi (Rai)

### Material
* Latest changes, architecture updates: 
  * Causeway Link: https://member.5g-mag.com/wg/TF-RT/document/559
  * Google Drive Link: https://drive.google.com/file/d/1MwaWquOw6WEh0vM_LAarXZ5EC90Xs8JG/view?usp=sharing  
* Code flow diagram: 
  * Causeway Link: https://member.5g-mag.com/wg/TF-RT/document/560
  * Google Drive Link: https://drive.google.com/file/d/1JjDFZ6xpG9vLbzVbfN_C3Krenjk2lrrt/view?usp=sharing

### Notes - Code overview
* Overall: From antenna to Big Buck Bunny
* SDR hardware, tested with BladeRF and LimeSDR
* If additional SDR cards are required a driver for SoapySDR is required
* MBMS Modem
  * SDR Reader
    * tune frequency 
    * take samples from the file and puts them in the ring buffer
  * Moving to srslte 
    * Request samples from srsUI
    * Does everything necessary in the time domain to get a synchronized frame from the hardware
    * Standard srsLTE impmentation did not know about MBMS cells
  * CAS / MBSFN Frame processor
    * Running in threads in parallel
    * Lots of changes to corresponding srslte functions
      * OFDM
      * CHEST
    * Channel Decoders
      * Return actual data 
    * RRC
      * Most of the things the RRC is doing is ignored
      * Activates Bearers in the RLC
    * ASN
      * Code generated automatically, tool not available
    * GW
      * Stick IP packets to the tunnel interface
* MBMS Middleware
  * Libflute as a submodule
  * Flute Receiver
    * Constructs ALC packet of the data
  * Creates an Encoding Symbol and gets that back
  * Sticks the encoding symbol to file
  * RESTful API
    * Returns files to the video player
  *  Middleware
    * Main control bit
    * Calls the model REST API
    * Check if FLUTE channel contains a Service Announcement that starts receiving the FLUTE session

### Discussion items / Questions
* Q: Do we branch changes to srsLTE back to the main branch
  * No, they did a rename in the meantime to srsRAN, but would be nice to have. Apply our changes on top of the srsRAN
* Q: Decode more than one FLUTE channel?
  * Yes, limited by CPU but no particular limitation
* Q: Why write own FLUTE library
  * Started using existing one. Main problem was the licensing, should be compatible with existing 5G-MAG licenses
  * All libraries went through file system, wanted to have all in memory with less copying as possible
  * Memory buffer in FILE object. Nothing is copied ever
  * Own library benefitial also for CMAF chunked low latency streaming
* Q: Is FLUTE library implementing old FLUTE version
  * Klaus: No large differences between version 1 and 2
  * MBMS spec reference old specs of FLUTE and ALC
* Q: No content encoding and forward correction FLUTE library
  * Yes, potential work item for the future
* Q: If libflute would receive FEC packets would it throw an exception
  * Yes, potential contribution
* Q: All changes on srsLTE documented?
  * Yes
* Q: Can we use srsRAN from the transmitter side against our version of srsLTE
  * Not sure, might work. 
  * Jordi, Klaus: To add information here
* Q: Are the BaseURLs prioritized?
  * No not yet, something in the DVB spec
* Q: Would make sense to add a document what could be done on application and what on player level
  * Separate between player and application code
* Q: Might it be useful to ServiceWorker 
  * Yes might be useful but needs to be aware of FLUTE decoded files
* Q: Can we setup an end-to-end workflow
  * Research and documentation what is needed first and what can be reused in terms of open source software
* Q: Is SAND DASH specific
  * No, SAND messages can be used for DASH and HLS.


## Minutes 17.09.2021

### Participants
Jordi J. Gimenez (5G-MAG), Daniel Silhavy (Fraunhofer FOKUS), Johann Mika (ORS), Klaus Kuehnhammer, Ali C. Begen (Comcast),kovacse,Álvaro (UPV) (Alvarito), Ben Smith (EDT), Joseph Collins [RTL NEWS], Richard Bradbury ,Stefan Schinkel, Bob Campbell (Eurofins Digital Testing), Kárpáti Rudolf, Romain Bouqueau, Thomas Stockhammer, Federico Maria Pandolfi (Rai), Jonas Beöczy, Christophe Burdinat (ATEME),  Ian Medland, Elfed Howells, Tünde Kaufman, Louay Bassbouss (Fraunhofer FOKUS), Alessandro Lucco - RAI, Veronica Maiello, Richard Bradbury, Francesc Mas, cthienot, Alvaro (UPV), Thorsten Lohmar (Ericsson), Martin Holovlasky (KRONEHIT), Jaime Sánchez Roldán (UPV), Alireza Erfanian, Christoph Tanzer (ORS) , john elliott

### Slides
https://drive.google.com/drive/u/0/folders/1YsYY8zkAvEEq2tapUNtYw9mUCnY8DuMI

### Notes
* Discussion which specifications to link on https://github.com/5G-MAG/Documentation-and-Architecture/projects .
  * Decided to link the ones that were used for implementation
  * Thorsten: Might be good to link 26348, currently only xMB stage 3 is linked
  * See: https://member.5g-mag.com/wg/TF-RT/wiki/48
* Discussion on the architecture illustrations
  * Separate illustration for 5G MS, 5G BC and hybrid approach
  * Consider splitting the diagrams up 
* Discussion if changes to the players (dash.js, hls.js) are required, especially for low latency
  * Ideally no changes to the player. Handling should be done in application
* 5GMS Client can use MBMS-API
* Next call on October 8th. Focus on
  * Your contributions: What do you want to contribute to the existing code? What are your plans for the upcoming weeks/month?
  * Concrete questions regarding the current code. You will have the chance to ask anything that is not clear to the head developers.
  * Quick code overview: The head developers will give a quick overview of the current code.
  * The architecture: We will enhance and improve our existing architecture diagrams and discuss them with you.