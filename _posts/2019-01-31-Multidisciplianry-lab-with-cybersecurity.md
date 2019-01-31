


# Key notes from [Multidisciplinary Experiential Learning](https://sites.temple.edu/care/mel_pubs/)

---
### Aim:
The goal of the game is to maintain a constant grid frequency on your own microgrid through the implementation of a control system.

not only on understanding engineering and power grid concepts, but also on how to prepare and respond to cyberattacks by making dynamic decisions in groups

### Methods:
used an interactive microgrid simulator for hands-on understanding of grid operations and security (microgrid stability, generation control system, generator inertia, energy storage, and grid security) 
Multi-dicpline: criminal justice, organizaiton,.. human reaction.

### Background:
* US power grid
* The grid is considered to be “target number one” for cyberattacks and cyberterrorism as power disruptions can impact other dependent infrastructures and have significant economic ramifications and industry downtime costs [14, 16, 20, 22] in [1]

### Real Cases:
* denial-of-service attack on a German power utility in 2012 that shut down the load power for five days
* the sniper attack on a California substation that damaged 17 large power transformers
* 2015 Ukranian grid cyberattacks (The malware known as Blackenergy was used by cybercriminals to shut down the power for a few hours while making the targeted hardware unbootable ) [5]

### Key Highlight:
Security of power grids can be modeled as a three layer process: **infrastructure security, information security, and control system security** .
>Infrastructure security deals with the protection of physical and cyber assets of the grid, such as SCADA systems, RTU’s, various sensors, and computers etc., which is achieved by firewalls, authentication processes, and various other methods. Information security pertains to the protection of information by encryption, digital signature, authentication codes, etc. Control system security is the application layer of power grid protection usually based on mathematical and control theoretic tools for robust intrusion tolerant algorithms.

 system observability and state estimation are the subject

---

### Attack:
DoS(block communication), phishing(espionage or destructure), spoofing(intercepting data, fool the operatotor)
Stuxnet[4] realworld cypberattack used by US.
false data injection[6].
*Data Integrity attack*


Their countermeasures:
up-to-date anti-virus , intrusion detection system (IDS)..., wipe out os.


### Game overlook 
Whilst maintaining the microgrid, the player must purchase more power generation sources as well as garner more customers to maximize their score/revenue. The players undergo a series of random attacks throughout the duration of the gameplay, much like a real power grid. The players must make an effort to fortify their defenses to the best of their abilities or else they will be shutdown resulting in a loss for that particular game session

---

Resilient Control System
![Resilient Control system[2] ](https://user-images.githubusercontent.com/3467118/51581671-1ea2a900-1f1d-11e9-8ff6-9dc7ff54eada.png)

Grid Grame
![grid game screenshot [3] ](https://gridgame.inl.gov/screenshots/9E2604F4-73A8-47CB-BBCA-1AD000AA9EB7.png)


---
### Cyberattack Model in their course scope
Team of Electronic engineering student steering the simulate smart grid system
Team of Criminal justice students schedule cypberattack for the defender.

The attack type and damge and remedy is summarized, defender focus more on the economic effects.
e.g., buying anti-virus, manual control the power grid.

[1].[Using Simulators to Assess Knowledge and Behavior
of “Novice” Operators of Critical Infrastructure under
Cyberattack Events](http://gridgame.ironforidaho.net/downloads/ResWeek_2017_ECE_final.pdf)

[2]https://www.osti.gov/servlets/purl/1360738

[3].GRID game: https://gridgame.inl.gov/

[4].https://en.wikipedia.org/wiki/Stuxnet

[5].[BlackEnergy trojan strikes again: Attacks Ukrainian electric power industry](https://www.welivesecurity.com/2016/01/04/blackenergy-trojan-strikes-again-attacks-ukrainian-electric-power-industry/)

[6].Y. Liu, P. Ning , and M. Reiter, “False Data Injection Attacks against State Estimation in Electric Power Grids,” ACM Trans on Information and System Security, Vol. 14, May 2011.


