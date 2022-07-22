# PLL-IC-Design
8x PLL Clock Multiplier IP on the Google-Skywater 130nm node
# Phase-Locked Loop IC design using Open-Source PDK
<img src = "general/pll_workshop.png" width="90%" height="90%">
This repository reflects the work done in the PHASE-LOCKED LOOP (PLL) IC Design using Open-Source PDK workshop, offered by VLSI System Design Corp. Pvt. Ltd in collaboration with Lakshmi Sathi. It is a 2-day workshop aiming to familiarise ourselves with open source tools, PDKs, Analog Design, and TapeOut Process under efabless.com. Furthermore, if you are a beginner, and visiting this GitHub repository, you can follow these steps to Design and Layout your own PLL IC.

PLL- Need: To generate clk signal with spectral purity which is otherwise difficult to achieve with only VCO(Voltage Controlled Oscillator) or only Quartz crystal.

<img src="https://user-images.githubusercontent.com/94942531/154814007-fdf1c555-38b8-4db6-aa16-fcf1ecf8f0e0.jpg" width="800" height="400" />

Parts of PLL block diagram:
- Phase frequency detector: Does the comparison of reference with output signal
  - It has 2 output signals, up signal to increase the frequency and down signal to decrease the frequency of the output signal of PLL

- Charge pump: Converts output of PFD signal to analog signal
  - It gives analog equivalent of up and down signal by either increasing or decreasing the voltage magnitude 
  - <img src="https://user-images.githubusercontent.com/94942531/154814058-aa8071a9-cf8d-4714-97cd-b613777beaa5.jpg" width="600" height="400" />

- Low pass filter: Multiple aspects, primarily to smoothen the output signal and also to stabilize the circuit.
  - <img src="https://user-images.githubusercontent.com/94942531/154818198-ee265ae5-fc26-4a90-a59b-ade60e951c69.jpg" width="600" height="400" />

- Voltage controlled oscillator: Generation of clk signal as per the analog signal from charge pump.
  - <img src="https://user-images.githubusercontent.com/94942531/154814094-89523427-7746-4571-8acf-90304cc382e8.jpg" width="600" height="400" />
- Frequency divider: Coefficient value of this divider decides the clock multiplier value of PLL. If divider is dividing the output clock by 8 then, PLL can multiply the input signal by 8.
  - <img src="https://user-images.githubusercontent.com/94942531/154817567-229b4bc0-6047-4af5-a11f-1c3337e8aa94.jpg" width="600" height="400" />



Specifications:
- Supply voltage: 1.8V
- Temperature: room temperature
- Process corner: TT
- Reference clock signal: Fmin = 5MHz and Fmax = 12.5 MHz
- Duty cycle: 50%
- Multiplier: 8x  

Pre-layout simulations:

Frequency divider circuit:
Blue signal= input clock 
Red signal = Output of clk divider

<img src="https://user-images.githubusercontent.com/94942531/154800201-39ac9d1c-674b-480b-8fe4-ca1422107b50.png" width="500" height="400" /> 

Charge pump circuit:

Graph of output voltage due to current leakage (and when no input is applied): 
<img src="https://user-images.githubusercontent.com/94942531/154800347-276a79de-492e-434a-85cf-8d11be850bc3.png" width="500" height="400" />

Graph of output voltage for up signal:

<img src="https://user-images.githubusercontent.com/94942531/154800742-3dfea931-3704-4a70-9175-0b3ea388631a.png" width="500" height="400" />

Graph of output voltage for down signal:

<img src="https://user-images.githubusercontent.com/94942531/154800888-ef5f40d7-86a3-4ae1-8514-82204d278f90.png" width="500" height="400" />

Voltage Controlled Circuit:

<img src="https://user-images.githubusercontent.com/94942531/154801702-6b859d78-ff80-4c65-9700-2ec001850dc3.png" width="500" height="400" />

Phase Frequency Detector circuit:

<img src="https://user-images.githubusercontent.com/94942531/154801868-74986f19-c842-4866-ba7b-933af5e733ae.png" width="500" height="400" />

Final simulation of PLL circuit:

<img width="718" alt="image" src="https://user-images.githubusercontent.com/94942531/154839542-6742a44b-089e-4da3-ab21-c9b1762cc3d3.png">

And the zoomed-in version at the end of the waveform where the charge pump output is stable:

<img src="https://user-images.githubusercontent.com/94942531/154839329-ea5e297d-bf87-4047-9ab3-ee96386992c1.png" width="600" height="400" />

In the above image, it can be seen that we are getting the periodic output of up and down signals even when the charge pump output is stable. So, it looks like the PLL is not correctly locked. The reason behind this is unknown. Here, few approaches to allow PLL to be locked are explored.

Two possible approaches were assumed:
- VCO output frequency is not stable
- Input to VCO (Charge pump/loop filter output is fluctuating)
 
The first option is explored to good extent and is described below. 

 - Firtly, tried to use ac analysis to plot the frequency domain plot of the output clk signal of VCO but, it was not possible to get the frequency domain plot using ac analysis without an ac source in the design. Then, another approach was to use FFT (Fast Fourier Transform) to get the frequency domain plot and, was getting this output (this graph is the simulation output of VCO only and it's around 28MHz):

<img src="https://user-images.githubusercontent.com/94942531/159157417-79dc3fe3-cdce-4a51-89f7-66d1539693a9.png" />

The above graph is achieved with following statements in control block:

<img src="https://user-images.githubusercontent.com/94942531/159158423-8d077192-e289-49e2-a854-bfdb38323d0b.png" />

There are still unanswered questions like why do we have a bump at the start of the graph at 0MHz where there should be no DC at the output.

So, finally got the clock output with the transcient analysis with the complete prelayput simulation of the PLL circuit at different clock samples.

At 800th VCO output clk signal sample:
<img src="https://user-images.githubusercontent.com/94942531/159157785-0f4a8b84-1305-4f9f-8e33-0bd890ee5e51.png" />

At 3000th VCO output clk sample:
<img src="https://user-images.githubusercontent.com/94942531/159157937-739d4c96-0a1d-46af-b04b-7b8cef4cb9db.png" />

At 6000th VCO output clk sample:
<img src="https://user-images.githubusercontent.com/94942531/159157962-6a15f445-af85-4110-aa54-81b48bc51cc2.png" />

At 10000th VCO output clk sample:
<img src="https://user-images.githubusercontent.com/94942531/159157969-2ec490ae-2309-4e4c-bdb1-e625d444e3a6.png" />

At 12000th VCO output clk sample:
<img src="https://user-images.githubusercontent.com/94942531/159157981-934f0eb0-f76f-4769-a1e4-aaf67d5715c4.png" />

At 13500th VCO output clk sample (Fref is the frequency of the reference clock signal):
<img src="https://user-images.githubusercontent.com/94942531/159157849-2b8dfdcb-7774-45a7-8f05-f9fb55547a75.png" />

The above measurements are taken with the help of .meas statment in ngspice used in following manner (there is a need of transcient analysis with control block for this to work):
<img src="https://user-images.githubusercontent.com/94942531/159158585-1f180e31-702a-4291-a63d-592423362e20.png" />

So, the analysis of VCO with different control voltages for above mentioned frequency output:

| Sr | Vcontrol input voltage (in volts) | Output clock frequency (in MHz) |
| ---| --------------------------------  | ------------------------------- |
| 1 | 0.65 | 63.72 |
| 2 | 0.67 | 87.48 |
| 3 | 0.68 | 101.91 |
| 4 | 0.6782 | 99.18 |
| 5 | 0.679 | 100.39 (The frequency that we want) |
| 6 | 0.6925 | 122.67 |
| 7 | 0.6935 | 124.5 |
| 8 | 0.6938 | 124.99 (The frequency that we are getting) |
| 9 | 0.694 | 125.5 |

The voltage difference between required input control voltage and actual control voltage 
Vdiff = 0.6938 – 0.679 = 14.8 mV 

Basically, we are getting 14.8 mV of extra voltage at the output of charge pump which is causing VCO to give output which is 125MHz. 
This could be related to the process node differences (The PLL specifications were originally planned for 180nm technology node but, the used technology node is 130nm)

## Noise analysis:

It was seen that the VCO output is not matching the reference clock input which can be seen below:

<img src="https://user-images.githubusercontent.com/94942531/159158924-88debf8b-6e64-4b9e-bb29-39e94257f1db.png" />

The zoomed-in version:

<img src="https://user-images.githubusercontent.com/94942531/159158930-96858087-9446-41e2-885e-e9dacad41937.png" />

In above image, we can see that, the reference clock signal is not aligned with the VCO clock output signal in the second half of clock cycle. This is achieved by using VCO and PFD (Phase Frequency Detector) circuits only. VCO input was fixed at 0.7V and the output was directly connected to one of the inputs of PFD circuit and another input was connected to reference clock signal. Then, this reference clock signal was manually tuned to match the output frequency of the VCO for 0.7V of control voltage input. This helped in determining the differences in the output of VCO with normal clock signal.

The misalignment was assumed to be due to phase noise that is inherent in VCO designs. To explore whether there is a phase noise in the output of VCO, noise analysis of the ngspice was expolred.

<img src="https://user-images.githubusercontent.com/94942531/159159302-ef08ad93-5cdb-4439-8c41-c338af7e1b7d.png" />

Got these results:

<img src="https://user-images.githubusercontent.com/94942531/159159325-7cf4a3df-daee-4a4e-a34e-0dd923ef505c.png" />

Total output noise in 1Hz to 100MHz range was nearly 800 uV.   

This graph shows the output noise spectrum plot: 

<img src="https://user-images.githubusercontent.com/94942531/159159332-ebd28c39-4c01-416e-ae09-a5fa72a3031a.png" /> 

The zoomed-in version of the same:

<img src="https://user-images.githubusercontent.com/94942531/159159338-088a7812-9ef7-4977-8201-1cf7c45ab8e1.png" />

However, the output that we see here is the ac small signal noise and not the phase noise as we had expected (this has also been confirmed with the ngspice community as well). 

# Acknowledgement
- Kunal Ghosh, Co-founder of VLSI System Design

## Table of Contents
- [About](#about)
- [DAY 1 : PLL Theory and Tools setup ](#day-1--pll-theory-and-tools-setup)
  - [PLL components](#pll-components)
  - [Phase Frequency Detector](#phase-frequency-detector)
  - [Charge Pump](#charge-pump)
  - [Low Pass Filter](#low-pass-filter)
  - [Voltage Controlled Oscillator](#voltage-controlled-oscillator)
  - [Tool setup and PDK](#tool-setup-and-pdk)
  - [Specifications](#specifications)
  - [Design Flow](#design-flow)
- [DAY 2: PLL Design and post-layout simulations](#day-2--pll-design-and-post---layout-simulations)
  - [PLL components circuit design](#pll-components-circuit-design)
  - [PLL components circuit simulations](#pll-components-circuit-simulations)
  - [Steps to combine PLL sub-circuits and PLL full design simulation](#steps-to-combine-pll-sub---circuits-and-pll-full-design-simulation)
  - [Troubleshooting steps](#troubleshooting-steps)
  - [Layout design](#layout-design)
  - [Layout Walkthrough](#layout-walkthrough)
  - [Parasitics extraction](#parasitics-extraction)
  - [Post Layout simulations](#post-layout-simulations)
  - [Steps to combine layouts](#steps-to-combine-layouts)
  - [Tape-out : A Brief Introduction](#tape-out--a-brief-introduction)
- [Future Scope](#future-scope)
- [Extra Reference Material](#extra-reference-material)
- [Acknowledgements](#acknowledgements)


## About
This workshop presents a basic overview of Phase-Locked Loop IC design using Open-Source Google-Skywater 130nm node, using an intuitive approach to designing a simple PLL with little math and without diving into complex frequency domain analysis and control system theory. Starting with basic PLL concepts, this overview spans all the steps in the IC design flow - circuit design, simulation, layout, parasitics extraction, post-layout simulation and finally, it also briefly includes the use of the latest caravel harness to make tape-outs.


## DAY 1 : PLL Theory and Tools setup 
PLL is a control system implemented using an analog circuit, which is used in many applications like, FM modulation and demodulation circuits, motor speed controls and tracking filters, frequency shifting decodes for demodulation carrier frequencies, time to digital converters, Jitter reduction, skew suppression, clock recovery, to get a precise clock signal without frequency or phase noise. PLL actually mimics the reference means to have the same or a multiple of the reference frequency and a constant phase difference with it. In this workshop, our focus was to design a PLL for clock signal operations in different SoCs and ICs. PLL uses a Voltage Controlled Oscillator and gives an output that has superior spectral purity nearly equal to that of Quartz Crystal. This figure shows a PLL implemented on a chip with Raven SoC. Second picture shows On Semiconductor's PLL IC on a PCB.

<img src = "general/day_1/ravenSoc.png" width="45%" height="45%">    <img src = "general/day_1/HC4046A.jpg">



### PLL components
PLL has five main components, and they are:-
  - Phase Frequency Detector
  - Charge Pump
  - Low Pass Filter
  - Voltage Controlled Oscillator
  - Frequency Divider

<img src = "general/day_1/pll_components.jpg" width="80%" height="80%">

### Phase Frequency Detector
It compares the phase difference between the input signal and output signal and generates a pulse signal according to it. The width of the pulse is used to determine the phase of the signal. As for width increases, phase also increases. The PFD output voltage is used to control the VCO such that the phase difference between the two inputs is held constant, making it a negative feedback system. The dead zone is one of a problem with PFD. The phase difference below which PFD output is not able to reach the desired logical level and fails to turn on the charge pump switches is called the dead zone. To tackle this, one should use precision PFDs, which overcome this issue.

<img src = "general/day_1/pfd.png" width="30%" height="30%">     <img src = "general/day_1/pfdfunc.jpg" width="50%" height="50%">

### Charge Pump
The PFD generates a digital signal, but VCO uses an analog signal as input. So, here comes Charge Pump in the picture.
The charge pump converts the digital signal of PFD into analog control signal which is given as input to the VCO. This can be done using the current steering circuit. When the UP signal is active the capacitor gets charged, this increases the voltage at charge pump output. When the DOWN signal is active, the capacitor gets discharged through the ground. This output voltage controls the VCO. An increase in voltage, speeds up the oscillator, while a reduction in voltage, slows down the oscillator. 

<img src = "general/day_1/cp.png" width="25%" height="25%">   <img src = "general/day_1/cp_func_up.png" width="70%" height="70%">
<img src = "general/day_1/cp_func_down.png" width="70%" height="70%">

### Low Pass Filter
The main issue with the charge pump is charge leakage, it keeps charging the capacitor even when inputs are off. This is tackled by using Low Pass Filter in the output. After that, the low-frequency dc voltage signal is sent into a dc amplifier, which boosts the signal level. After that, the VCO receives the enhanced signal. This will smoothens the output, without LPF, PLL cannot lock and mimic the ref signal. Even though, the primary function of the LPF is to maintain the stability of the system. Mathematically, only one capacitor at the output of the Charge pump makes the system unstable because if we see the frequency domain analysis of the circuit then there will be two poles in the transfer function which makes it oscillating and highly unstable. Thus an RC LPF is added at the output such that output gets stabilize. The value of Cx should be roughly around one-tenth of C. The loop filter bandwidth must be less than one-tenth of the highest output frequency.

<img src = "general/day_1/lpf.png" width="25%" height="25%">

### Voltage Controlled Oscillator
VCO is the heart of PLL and it generates the desired high-frequency clock output. Voltage-controlled oscillators are the actual parts that produce alternating digital clock signals. The most common one is the Ring oscillator. It contains an odd number of inverters and flips the output. So, VCO can be implemented using simple inverters. It is necessary to design this VCO such that the range of output frequency we want for the PLL is within the range of frequency the VCO can produce properly. The frequency of this clock signal can be controlled by the input voltage. The frequency depends on delay and delay depends on the current supplied. The LPF output serves as a VCO control signal. Current sources are used at the top and the bottom with the Vctrl voltage to control the ring oscillator. An analog signal is produced by the VCO and its amplitude is proportional to the LPF output amplitude.

<img src = "general/day_1/vco.jpg" width="40%" height="40%">

### Frequency Divider
A frequency divider is used to convert the whole system into a frequency multiplier. A PLL with a frequency divider on its feedback loop is called a clock multiplier PLL. Such a PLL can make clock signals which are multiples of the reference signals. A toggle flip flop generates the clock which has twice the time period of the input clock given to it, that is we obtain the clock having half the frequency of the input clock provided to the frequency divider. For the 8x clock multiplier, we should divide the output clock by 8 to generate a feedback clock. For obtaining one-eighth of frequency, we should cascade three toggle flip flops. When the frequency difference of each input is 0, which shows a consistent phase difference, the loop is locked. If the two signals are shifted by 180, the output voltage must be at the highest. The output voltage generated will be zero in the absence of an input signal to allow the VCO to function at a fixed frequency. This frequency is referred to as the oscillator's operating frequency.
Lock Range: The range of frequencies for which PLL can maintain lock, already in the locked state.
Capture Range:  The frequencies for which PLL is able to lock in from an unlocked state.
Settling Time: The time within which the PLL is able to lock in the form of an unlocked condition.

<img src = "general/day_1/fd.jpg" width="30%" height="30%">

### Tool setup and PDK
The beauty of open-source is that it is free and available to everyone. In the whole workshop, I have used only two tools and one PDK. Ngspice is an open-source mixed-signal electronic circuit simulator used in circuit design, pre-layout, and post-layout simulation. Magic VLSI Layout Tool is an open-source circuit layout editor, used in circuit layout. Google SkyWater PDK is an open-source process design kit, used in physical design. To install these open-source tools, I would suggest you go to their websites:-
  1. [Ngspice](http://ngspice.sourceforge.net/)
  2. [Magic VLSI Layout Tool](http://opencircuitdesign.com/magic/)
  3. [Google/Skywater 130nm PDK](https://github.com/google/skywater-pdk)

IT IS RECOMMENDED TO USE LINUX-BASED OS RATHER THAN ANY OTHER KERNEL-BASED OS.

### Specifications
- Technology Node = 130nm
- Supply voltage (VDD) = 1.8V
- Minimum Input Frequency = 5MHz
- Maximum Input Frequency = 12.5MHz
- Multiplier = 8 times
- Jitter (RMS) < 20ns
- Duty cycle = 50%

### Design Flow
- Specifications of the IC
- SPICE level circuit development
- Pre-layout simulation
- Layout development
- Parasitic Extraction
- Post-layout simulation
- Tapeout: A brief Introduction


## DAY 2: PLL Design and post-layout simulations
On day 2, we are going to design different components of PLL at the transistor level, then we will simulate it in Ngspice. Check all the desired outputs, and then proceed with Layout in Magic followed by parasitics extraction. After all this, we will again do the post-layout simulation in Ngspice. After all of this, we will combine different components of PLL in a single layout design. At last, we will integrate the layout design of PLL with Caravel SoC. In the end, we will discuss about tape-outs, and know-how any individual can send their own layout design to The Google-Skywater open MPW shuttle program, to be manufactured on a silicon wafer. But it depends on the choice of Google and Skywater to whether manufacture or not.

### PLL components circuit design
The transister level design of Phase Frequency Detector, Charge Pump, and Voltage Controlled Oscillator, respectively are as follows:

<img src = "general/day_2/pfd.jpg" width="25%" height="25%"> <img src = "general/day_2/cp.jpg" width="30%" height="30%"> <img src = "general/day_1/vco.jpg" width="40%" height="40%">

### PLL components circuit simulations
Pre-Layout Ngspice Simulation of Phase Frequency Detector Circuit, with DOWN and UP Signal:

<img src = "general/day_2/pdf_pre_down.jpg" width="45%" height="45%">   <img src = "general/day_2/pdf_pre_up.jpg" width="45%" height="45%">

Red: Clock 2, Blue: Clock 1, Orange: Up Signal, Green: Down Signal

##################################################################

Pre-Layout Ngspice Simulation of Charge Pump + Low Pass Filter Circuit, with Charging, Discharging, and Leakage:

<img src = "general/day_2/cp_pre_charge.jpg" width="33%" height="33%">  <img src = "general/day_2/cp_pre_discharge.jpg" width="33%" height="33%">   <img src = "general/day_2/cp_pre_discharge.jpg" width="33%" height="33%">
Red: Charge Pump Output Voltage, Leakage: 40uV increase every 1us

##################################################################

Pre-Layout Ngspice Simulation of Frequency Divider Circuit:

<img src = "general/day_2/fd_pre.jpg" width="50%" height="50%">
Red: Output Clock, Blue: Input Clock

##################################################################

Pre-Layout Ngspice Simulation of Voltage Controlled Oscillator:

<img src = "general/day_2/vco_pre.jpg" width="50%" height="50%">
Red: Input Signal, Blue: Output Signal

### PLL full design simulation
Pre-Layout Ngspice Simulation of Full PLL Circuit:

<img src = "general/day_2/full_pll_pre.jpg">

Red: Reference Clock, Blue: Output Clock Divided by 8, Yellow: Down Signal, Brown: Up Signal, Pink : ChargePump output

#################################################################################

Close-UP Simulation Result of Full PLL Circuit:

<img src = "general/day_2/close_up_pre.jpg" width="60%" height="60%">

### Layout design
Layout design of Phase Frequency Detector Circuit:

<img src = "general/day_2/pfd_layout.jpg" width="60%" height="60%">

Layout design of MUX:

<img src = "general/day_2/mux_layout.jpg" width="60%" height="60%">

Layout design of Charge Pump + Low Pass Filter Circuit:

<img src = "general/day_2/cp_layout.jpg">

Layout design of Frequency Divider Circuit:

<img src = "general/day_2/fd_layout.jpg">

Layout design of Voltage Controlled Oscillator:

<img src = "general/day_2/vco_layout.jpg" width="60%" height="60%">

### Layout Walkthrough
Layout design of all components integrated into a single design i.e Full PLL Layout Design
<img src = "general/day_2/pll_layout.jpg">

### Post Layout simulations
Post-Layout Ngspice Simulation of Phase Frequency Detector Circuit, with DOWN and UP Signal:

<img src = "general/day_2/pfd_post_down.jpg" width="45%" height="45%">   <img src = "general/day_2/pfd_post_up.jpg" width="45%" height="45%">

Red: Clock 1, Blue: Clock 2, Orange: Up Signal, Green: Down Signal

###############################################################################

Post-Layout Ngspice Simulation of Charge Pump + Low Pass Filter Circuit, with Charging, Discharging, and Leakage:

<img src = "general/day_2/cp_post_charge.jpg" width="33%" height="33%">  <img src = "general/day_2/cp_post_discharge.jpg" width="33%" height="33%">   <img src = "general/day_2/cp_post_leakage.jpg" width="33%" height="33%">
Orange: Charge Pump Output Voltage, Red: Up Signal, Blue: Down Signal, Leakage: < 0.05V in 100us

################################################################################

Post-Layout Ngspice Simulation of Frequency Divider Circuit:

<img src = "general/day_2/fd_post.jpg" width="50%" height="50%">
Red: Input Clock, Blue: Output Clock

#####################################################################################

Post-Layout Ngspice Simulation of Voltage Controlled Oscillator:

<img src = "general/day_2/vco_post.jpg" width="50%" height="50%">
Red: Input Signal, Blue: Output Signal

##################################################################################

Post-Layout Ngspice Simulation of Full PLL Circuit:

<img src = "general/day_2/full_pll_post.jpg">

Red: Reference Clock, Blue: Output Clock Divided by 8, Yellow: Down Signal, Brown: Up Signal, Pink : ChargePump output

#########################################################################################

Close-UP Simulation Result of Full PLL Circuit:

<img src = "general/day_2/close_up_post.jpg" width="60%" height="60%">

### Tapeout : A Brief Introduction
For any design to be tape-out ready there are more requirements than just having model files, simulation results, layout designs, and .gds files.For Example, a proper GPIO (cells that enable the IP to be interfaced with the external world) is needed for connecting the IP pins to the package (the final DIP or Surface Mount case from which the IC comes in from the Fab).To meet these requirements either we need to take care of them individually by ourselves (which may get complicated and time-consuming) or, we can choose a vehicle for enabling our IP to meet the requirements to go through the fabrication process. Here we will be using [Efabless Caravel SoC template](https://github.com/efabless/caravel) as the Vehicle. This is the [datasheet](https://github.com/efabless/caravel/blob/master/doc/caravel_datasheet.pdf) of the Caravel SoC from [Efabless](https://efabless.com/), and these are the parts involved in it:
<img src ="general/day_2/CaravelSoCTemplate.jpg" width="60%" height="60%">

The Mega Project Area (MPRJ) or 'user_project_wrapper' or in other words 'the container' is where we will place and route our design.
Basic Steps Overview:
- Initial setup.
- Place and Route the IP inside the container (keep in mind to not have any DRCs).
- Verify Connectivity.
- Integrate the container onto the Caravel SoC.
- Check if everything is as expected including DRC (Precheck).

## Future Scope
- Work on getting specifications and change your designs according to them.
- After changing in your design, you can make this design as your own IP.
- Integrate the IP with Caravel SoC, and send it to Google-Skywater Open MPW Shuttle Program, to get a real IP on a silicon wafer.

## Extra Reference Material
- [Magic command reference. Here, press the 'Using Magic' button](http://opencircuitdesign.com/magic/)
- [Caravel SoC Documentation](https://caravel-harness.readthedocs.io/en/latest/description.html)
- [NPTEL Course on Analog IC Design by Nagendra Krishnapura](https://nptel.ac.in/courses/117/106/117106030/)
- [NPTEL Course on RF Integrated Circuits by Shouri Chatterjee](https://nptel.ac.in/courses/117/102/117102012/)
- [An Improved Performance Ring VCO: Analysis and Design](https://www.researchgate.net/publication/330244405_An_Improved_Performance_Ring_VCO_Analysis_and_Design)
- [A Novel Phase Frequency Detector for a High-Frequency PLL Design](https://www.sciencedirect.com/science/article/pii/S187770581301624X)
- [A pA-leakage CMOS charge pump for low-supply PLLs](https://ieeexplore.ieee.org/document/5548821)
- [A simple and high performance charge pump based on the self-cascode transistor](https://dl.acm.org/doi/abs/10.1007/s10470-019-01478-y)
- [Gain-Boosting Charge Pump for Current Matching in Phase-Locked Loop](https://ieeexplore.ieee.org/document/1715569)
- [Comparative Analysis of High Speed FBB TSPC and E-TSPC Frequency Divider at 32 nm CMOS process](http://www.ijtrd.com/papers/IJTRD5427.pdf)

## Acknowledgements
- [Lakshmi S](https://github.com/lakshmi-sathi), MS in ECE, Georgia Tech
- Kunal Ghosh, Co-founder, [VSD Corp. Pvt. Ltd.](https://www.vlsisystemdesign.com/)
