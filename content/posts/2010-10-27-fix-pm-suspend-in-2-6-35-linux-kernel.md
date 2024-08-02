---
title: 'fix pm-suspend in >= 2.6.35 linux kernel'
author: vbatts
layout: post
date: 2010-10-28T01:45:58+00:00
url: /2010/10/fix-pm-suspend-in-2-6-35-linux-kernel/
aktt_notify_twitter:
  - yes
  - yes
aktt_tweeted:
  - 1
  - 1
tags:
  - Linux
  - slackware
tags:
  - Linux
  - slackware

---
For those with a newer laptop, in my case a Lenovo, the NEC USBv3 port is cool, unused and so far, a pain in the ease of my mobile life.

The hardware info (from lspci -nnvv) is:
  

`
<pre>0f:00.0 USB Controller [0c03]: NEC Corporation Device [1033:0194] (rev 03) (prog-if 30)</pre>
<pre> Subsystem: Lenovo Device [17aa:219c]</pre>
<pre> Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR+ FastB2B- DisINTx-</pre>
<pre> Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-</pre>
<pre> Latency: 0, Cache Line Size: 64 bytes</pre>
<pre> Interrupt: pin A routed to IRQ 18</pre>
<pre> Region 0: Memory at f2200000 (64-bit, non-prefetchable) [size=8K]</pre>
<pre> Capabilities: [50] Power Management version 3</pre>
<pre> Flags: PMEClk- DSI- D1- D2- AuxCurrent=375mA PME(D0+,D1-,D2-,D3hot+,D3cold+)</pre>
<pre> Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=0 PME-</pre>
<pre> Capabilities: [70] MSI: Enable- Count=1/8 Maskable- 64bit+</pre>
<pre> Address: 0000000000000000  Data: 0000</pre>
<pre> Capabilities: [90] MSI-X: Enable- Count=8 Masked-</pre>
<pre> Vector table: BAR=0 offset=00001000</pre>
<pre> PBA: BAR=0 offset=00001080</pre>
<pre> Capabilities: [a0] Express (v2) Endpoint, MSI 00</pre>
<pre> DevCap: MaxPayload 128 bytes, PhantFunc 0, Latency L0s unlimited, L1 unlimited</pre>
<pre> ExtTag- AttnBtn- AttnInd- PwrInd- RBE+ FLReset-</pre>
<pre> DevCtl: Report errors: Correctable- Non-Fatal- Fatal- Unsupported-</pre>
<pre> RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop+</pre>
<pre> MaxPayload 128 bytes, MaxReadReq 512 bytes</pre>
<pre> DevSta: CorrErr- UncorrErr- FatalErr- UnsuppReq- AuxPwr+ TransPend-</pre>
<pre> LnkCap: Port #0, Speed 5GT/s, Width x1, ASPM L0s L1, Latency L0 <4us, L1 unlimited</pre>
<pre> ClockPM+ Surprise- LLActRep- BwNot-</pre>
<pre> LnkCtl: ASPM L1 Enabled; RCB 64 bytes Disabled- Retrain- CommClk+</pre>
<pre> ExtSynch- ClockPM+ AutWidDis- BWInt- AutBWInt-</pre>
<pre> LnkSta: Speed 2.5GT/s, Width x1, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-</pre>
<pre> DevCap2: Completion Timeout: Not Supported, TimeoutDis+</pre>
<pre> DevCtl2: Completion Timeout: 50us to 50ms, TimeoutDis-</pre>
<pre> LnkCtl2: Target Link Speed: 5GT/s, EnterCompliance- SpeedDis-, Selectable De-emphasis: -6dB</pre>
<pre> Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-</pre>
<pre> Compliance De-emphasis: -6dB</pre>
<pre> LnkSta2: Current De-emphasis Level: -3.5dB</pre>
<pre> Capabilities: [100] Advanced Error Reporting</pre>
<pre> UESta:  DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-</pre>
<pre> UEMsk:  DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-</pre>
<pre> UESvrt: DLP+ SDES+ TLP- FCP+ CmpltTO- CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+ ECRC- UnsupReq- ACSViol-</pre>
<pre> CESta:  RxErr- BadTLP- BadDLLP- Rollover- Timeout- NonFatalErr-</pre>
<pre> CEMsk:  RxErr- BadTLP- BadDLLP- Rollover- Timeout- NonFatalErr+</pre>
<pre> AERCap: First Error Pointer: 00, GenCap- CGenEn- ChkCap- ChkEn-</pre>
<pre> Capabilities: [140] Device Serial Number ff-ff-ff-ff-ff-ff-ff-ff</pre>
<pre> Capabilities: [150] #18</pre>
<pre> Kernel driver in use: xhci_hcd</pre>
<pre> Kernel modules: xhci-hcd</pre>
<p>`
  

  
Up until the 2.6.35 kernel, this had been using the kernel driver &#8220;xhci&#8221;, but in >= 2.6.35, it has been renamed &#8220;xhci_hcd&#8221;.

What does this have to do with anything?  The ease of my suspending to disk. This USBv3 driver is not yet playing well with the freezing/thawing process, and will give a cryptic error in /var/log/pm-suspend.log, pointing you to line 295 of `/usr/lib${LIBSUFFIX}/pm-utils/pm-functions`. When it gets to the point of executing `` `echo -n "mem" > /sys/power/state` ``, it returns a permission denied. Even though the contents of /sys/power/state show something like &#8220;mem disk&#8221;.

So for pm-utils, if you

  * go to `/etc/pm/config.d/`
  * create a file called &#8220;_unload_modules_&#8220;, with contents of
  * `SUSPEND_MODULES="xhci xhci_hcd"`
  * it&#8217;s a space delimited value, so add others if you are having issues.

TA DA. now i can resume the comfort of a quick suspend and resume!

Take care,

vb<!--more-->