# THRACE-1  
### A student designed and built CubeSat designed to measure the thermal and radiation environment around Earth, as well as testing consumer grade sensors in space. 
This is a satellite that is meant as a way to learn how spacecraft are built to gain a better understanding of how to build more advanced systems. It is an almost entirely custom made 2U CubeSat with very simple and inexpensive systems so that it is easier to build, since I am a high school student making this. This will measure surface temperatures on all 6 sides, particle radiation, and the local magnetic field, as well as measuring how well off the shelf components work in space compared to other proven sensors.

## Mission Overview
I am targeting a 45 degree inclination orbit at roughly 500km for a mission duration of 1-2 years. It is not SSO since it is trying to collect data all around the earth, as well as making the radiation problem a little easier. The satellite combines the temperature, radiation, and magnetometer readings with GNSS measurements to create a fully georeferenced dataset surrounding the whole earth. These measurements can be used to verify the shielding and heat management systems used compared to pre-flight simulations. There are also a very low end consumer grade MEMS IMU in there (the LSM6DSO lmao) to see how it really perform in that type of environment since I was curious to see how it would behave compared to the IMU on the CubeADCS system that I will be using. There is also a GNSS sensor that I use on my HPR flight computers, the SAM-M10Q from U-blox to see how it does compared to a more traditional SkyTraq Venus838. For a more detailed overview, look at the Mission Statement document in the repo.
This is just the prototype so I can learn and figure out how these things come together, before building the flight unit later on, fixing the mistakes I inevitably will make here :/

## Mission Objectives
**OBJ-1:** Map radiation dose rate vs. orbital position using a Geiger-Müller tube.

**OBJ-2:** Characterize the thermal environment across orbits via per-face RTDs.

**OBJ-3:** Map the local geomagnetic field and compare to the IGRF model.

**OBJ-4:** Validate the passive radiation shielding design against measured dose.

**OBJ-5:** Validate the thermal-control design against predictions.

**OBJ-6:** Demonstrate reliable operation of custom electronics (OBC and EPS) in space.

**OBJ-7:** Monitor radiation-induced drift in a reference MEMS IMU, correlated with particle flux.

**OBJ-8:** Characterize the radiation susceptibility of a commercial-grade MEMS IMU (LSM6DSO).

**OBJ-9:** Evaluate a consumer GNSS receiver (u-blox SAM-M10Q) at orbital velocity.

## System Architecture
1. **Custom OBC.** Derived from a newer version of my own avionics system I made for my High Power Rockets, I wanted to make sure that I design the most practical solution that matches my history. There is no reason to go out and make something completely unrelated to what you already know without good reason, and this project is hard enough. This uses a massive, 144 pin STM32H753 processor, containing dual flash for redundant storage of data when it can't be transmitted back, hardware crypto for authenticated uplink, and more peripherals and GPIO than you could ask for; it feels like I could add 100 different objectives to this thing before I worry about running out of GPIO. It takes 3S from the EPS for power, regulating down to 5V for the geiger tubes and 3.3V for everything else. The SAM-M10Q GPS and LSM6DSO are mounted here.
2. **Custom EPS.** MPPT solar input, 3S Li-ion battery management, switched power distribution, per-rail current sensing. Not too much to say here yet, will get more detailed when I start designing it as long as i remember to keep updating this.
3. **Commerical CubeADCS.** CubeSpace CubeADCS — reaction wheels + magnetorquers, sun-pointing. also not really much to say here.
4. **Satellite Comms.** Endurosat UHF Type II transceiver, deployable turnstile antenna, SatNOGS-compatible (you will find out why that's important soon).
5. **Payload** Geiger-Müller tube (isolated HV volume because it is so noisy especially with the step up converter omg), 6× PT1000 RTDs, dual IMU, dual GNSS, solar irradiance sensor.
6. **Structure** 2U Al6061 chassis with polyethylene radiation liner, MLI, deployable solar wings on each side with the one in the middle being static to make this big solar panel face, coated radiator on bottom face.
7. **Custom Ground Station** DIY SatNOGS rotator (see this is why the SatNOGS thing is important!!), RTL-SDR + LNA, Raspberry Pi, InfluxDB + Grafana telemetry pipeline, Cesium 3D visualization. **(ON HOLD FOR NOW)**

*Block diagram: see docs/thrace1_block_diagram_revB.svg*

## Engineering Highlights

**Flight-heritage-derived OBC.** The on-board computer (OBC) evolves from a custom high power rocketry flight computer (Mixer) into a spacecraft controller. It uses the same STM32H7 platform, dev board, so that firmware patterns carry across both.

**No single point of data loss.** Telemetry is written to two redundant SPI flash chips with read-back verification, protecting the only on-board data store against radiation-induced corruption.

**Robust power front-end.** Reverse-polarity protection, seamless USB/battery power muxing for bench operation, and local point-of-load regulation let the OBC run standalone on a USB cable during development without any losses.

**Accurate distributed thermal sensing.** Six PT1000 RTDs in 3-wire configuration, digitized close to the processor, map the thermal environment across all faces.

**End-to-end data path.** The same telemetry pipeline (radio → decoder → database → dashboard) is used for both ground testing and flight, so the ground segment is validated against the real data path rather than a shortcut.

## File Structure

```
\OBC
  OBC.kicad_pcb
  OBC.kicad_pro
  OBC.kicad_sch
BOM notes.txt
README.md
ThRACE-1_Mission_Statement_Rev1.2.pdf
thrace1_block_diagram_revB.svg
```

*will update as project develops*

## Personal Background
My name is Cortland, and I started this project when I was just finishing my sophomore year in high school. After a busy summer, I am starting to pick this project back up as I start my Junior year, and I hope to get this done before college apps so I can show this as a way to say "hey, I'm really interested in this whole space thing and this is what I did to prove it." Are there easier projects than this? absolutely. I could have a made a TVC controlled model rocket (which is something I'm interested in that I will do sometime, just after this whole CubeSat thing) but everyone does TVC now, so that wouldn't work. This is when I got super interested in interstellar spacecraft, and one night ended up talking to claude and some actual people and set up a pretty rough mission structure for two crafts to go to the alpha centauri system. What better way to build up to such a massive mission than to start with a small CubeSat to learn about to build for space. That is what this really is. For as long as I can remember I have been fascinated by space, and always wanted to put something up there. I designed and launched my first custom model rocket in 2021, and got my Tripoli Mentoring Program certification when when I was 12, the youngest age you are allowed to get it. I 3D printed rockets, made an AV bay out of this clear plastic, kind of just did whatever I felt would be cool.

In terms of electronics, I pretty much forced myself to get into it since it was necessary for what I wanted to do. I went to a little coding place where a lot of my foundation in python, HTML/CSS, and arduino was formed. I even learned a little game dev there. I found joy in making these systems work and working with people to solve these problems, and there's even some of the simple projects I made on other repos associated with this account. I first started trying to make my own PCBs in Eagle (RIP) after seeing a bunch of BPS.Space videos, and after a couple years I started to actually figure out what I was doing. I made my first fully surface mount PCB my sophomore year, and at the time of writing this it is actually about to fly on a rocket once I get it soldered (I had a busy summer so I couldn't work on it).

Anyways this was kind of just an infodump and I didn't really have a structure when I started writing this so if you are reading this, thank you! I am super excited about this project and I hope to learn a lot of cool stuff. I always had issues with staying focused on projects and procrastination, and i am hoping to fix that here by fixing my mentality about working in general. Whenever I would encounter a really frustrating issue I would always get up and walk in circles and then just like sit and watch youtube to stop thinking about it which sounds kind of weird but so far with this project it has been a lot better. Anyways, I feel great about this project, and if you are a college seeing this, don't worry about my grades, I mean who really cares about my algebra 2 grade when I'm building a cubesat.. right?

## License
MIT for code, CC-BY for documentation/hardware
