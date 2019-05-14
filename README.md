# IEC61850SecurityDataset
This repository contains network traces that describe GOOSE communications in a substation. The reference one-line diagram used for generating the network traces is shown in Figure 1. It consists of 4-buses, and 18 LEDs. The line feeders (1-6) are connected to different loads while the rest of the feeders connect to other nearby substations to provide redundancy. The IEDs communicate among themselves using the GOOSE protocol defined in the IEC 61850 standard. Based on the substation diagram, we consider three scenarios to generate the network traces

![one-line substation diagram](one_line.png)


## Normal Scenario
There are 18 IEDs in substation including Line Feeder IEDs, Transformer Feeder IEDs, Bus IED and Under Frequency Load Shedding IEDs.
These IEDs send multicast packets every second to share and update their status. Under a normal scenario where there is no disturbance or attack, the sqNum of GOOSE will increment with every transmission of GOOSE frame, while the stNum and timestamp values remains unchanged.
## Disturbance Scenario

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


## Attack Scenario

We generated three variants of stNum attacks and a composite attack comprising stNum and Boolean modifications to demonstrate practical GOOSE attacks. These generated attacks were intermixed with normal GOOSE traffic from the Normal.pcapng file, which can be found under the Normal/No_Variable_Loading directory. The duration of each PCAP file is 10mins. The details are as follows.

**Name: StNum1.pcapng**

**Description:** Inject a high stNum value or slightly higher than the previously recorded stNum, where sqNum≠0.
	
	- LIED10 injects a GOOSE frame with stNum=9999 and sqNum=10 at time= 13.9 sec.
 	
	- LIED12 injects a GOOSE frame with stNum=5 and sqNum=15 at time= 18.9 sec.

**Name: StNum2.pcapng**

**Description:** Replay a previously valid GOOSE frame containing high stNum, sqNum =0 but stale timestamp
	
	- LIED10 replays a GOOSE frame with stNum=9999 and sqNum=0 at time= 12.6 sec.

**Name: stNum3.pcapng**

**Description:** Inject a high stNum frame with sqNum = 0 and a valid timestamp.
	
	- LIED10 injects a GOOSE frame with stNum=9999 and sqNum=0 at time= 10.4 sec, where the timestamp of the injected GOOSE frame > previously received GOOSE frames.

**Name: Attack.pcapng**

**Description:** Inject high stNum attack followed by modifying the circuit breaker status associated with CB-11. 
	
	- LIED11 injects a GOOSE frame with stNum=9999 and sqNum=0 at time=11.3 sec
	
	- LIED11 modifies the Boolean value of CB-11 from ‘1’ to ‘0’ and injects the modified GOOSE frame at time=16.3 sec. 


## Others

**Scenario folder:**  each folder represents one scenario, it contains

	
1. One pcap file: captures GOOSE packets from 18 IEDs during 10 mins. 	
2. 18 csv files: list transmitted data from 18 IEDs at every second during 10 mins. But the attacking scenario is based on normal scenario's trasmission traffic, so CSVs in Attack folder are the same with Normal's.

**SCL folder:**
	
It contains 18 IID files to define configuration of 18 IEDs. It also discribes data exchange format from IEDs. From those file, you can understand the structure of payload in pcap files.


