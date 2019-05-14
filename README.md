# IEC61850SecurityDataset
This repository contains network traces that describe GOOSE communications in a substation. The reference one-diagram used for generating the network traces is shown in Figure 1. It consists of 4-buses, and 18 LEDs. The line feeders (1-6) are connected to different loads while the rest of the feeders connect to other nearby substations to provide redundancy. The IEDs communicate among themselves using the GOOSE protocol defined in the IEC 61850 standard. Based on the substation diagram, we consider three scenarios to generate the network traces

![one-line substation diagram](one_line.png)


######1. Normal Scenario

######2. Disturbance Scenario

We consider 3 representative disturbance scenarios under which substation protection system operates. 

**Busbar protection** 

1. Let us consider that a fault occurs at busbar 66kV bus-1. The incomer line LIED10 will pick up on overcurrent, the other IEDs wont.
2. The incomer line LIED10 will know through GOOSE communication that the overcurrent elements of other IEDs have not picked up.
3. The incomer line LIED10 will quickly realise of busbar fault and trigger a trip to its own breaker CB-10 first and subsequently to the breakers associated with the busbars i.e., CB-11, CB-12 and CB-13.
4. The trip status of CB-10 is sent by LIED10 through GOOSE communication to LIED11, LIED12 and TIED13 to trigger trip for their respective breakers.

**Breaker failure protection**

1. Assume that a fault occurs in the feeder connecting substation S/S 3-1. The associated LIED11 overcurrent (O/C) element picks up, however, the breaker CB-11 does not trip due to mechanical failure.
2. The GOOSE communication of breaker failure and O/C element pick-up is sent from LIED11 to the LIED10(incomer), LIED12 and TIED13.
3. The communication triggers tripping of circuit breakers CB-10, CB-12 and CB-13. Subsequently, the remote CB (in S/S 3-1) is tripped using proper communication media

**Underfrequencey load-shedding**
1. Under frequency IED (UFIED) can sense the under frequency in both 11kV buses when voltage transformer (VT) inputs are directly given to this IED.
2. Alternately, incomer line IEDs (LIED30 and LIED40) can relay the information to the under frequency IED over GOOSE (when there is no direct VT input to UFIED).
3. UFIED triggers a trip over GOOSE to the least priority (Priority 6) consumer first via LIED43. Then, CB-43 is tripped at first stage.
4. After a time delay (usually 2-4 seconds) if the frequency is not stabilized at the desired value, further loads are shed. The sequence of tripping goes as: Priority 6 (CB43) →Priority 5 (CB-42)→Priority 4 (CB-32) . . .
5. Between each two stages of tripping there is a time delay of 2-4 seconds. The trip is initiated via GOOSE communication to respective IEDs such as LIED43, LIED42, LIED32, etc.


######3. Attack Scenario


Each folder represents one scenario, it contains： 
	
	1. One pcap file: captures GOOSE packets from 18 IEDs during 10 mins. 
	
	2. 18 csv files: list transmitted data from 18 IEDs at every second during 10 mins.

But the attacking scenario is based on normal scenario's trasmission traffic, so there is no specific CSV in Attack folder.
