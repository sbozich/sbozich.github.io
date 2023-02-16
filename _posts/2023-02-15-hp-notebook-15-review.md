---
title: HP Notebook 15 review – computing on a budget
categories: [hardware, laptop]
tags: [hardware, hp notebook]
---

Some 18 months ago I got as a present a 15,6-inch HP laptop. Didn’t need a laptop, but an acquaintance of mine provided me with a new machine for some service I provided to him. Since I do vast majority of my computing on a desktop PCs (one exemption to that being that stubborn little <a href="https://sbozich.github.io/posts/thinkpad-x201-review/" target="_blank">Thinkpad</a>), I was curious how far a brand-new laptop can go in terms of performance, portability, connectivity and overall user experience.

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112301.jpg) 

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112302.jpg) _HP Notebook 15 gen. 2021_

## Specifications

Specs-wise, I was surprised when realized that the machine sports an AMD-processor. To be precise, it is an AMD Ryzen 5 3500U quad-core with HT (8 threads) with discrete GPU Radeon Vega 8, with 15W TDP 15W and 12nm lithography. That puts it somewhere in the lower-middle place on the market reserved for ultrabooks, mini PCs and budget laptops. There is 8 GB DDR4-2400 SDRAM (one piece, hence one-channel) and main storage 256 GB PCIe NVMe M.2 SSD. Sounds decent, although a bit tight. Given the tendency of soldering everything to the motherboard, I expected little-to-none upgrades possible; out of curiosity I immediately voided warranty (could afford being reckless as I have plenty of alternative PCs lying around) and unscrewed the back cover of machine to see the internals:

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112303.jpg) 

The RAM memory is NOT soldered and there are two slots, according to specs sheet max. supported amount is 16 GB (but the machine could probably take 32 GB as well given the chipset supports it); furthermore, there is space for 2.5” device (HDD/SSD) but that comes with some complications, later about that. I was hoping to find at least one internal USB connector (to put an USB mouse/keyboard controller into), but that was unjustified – there is no such thing inside. 

## Design, build quality, ports

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112304.jpg) 

The machine is not an ultrabook, but it is reasonably thin, so some scarcity of ports is expected. I supposed there will be an USB-C, but it is somehow missing (meaning no charging via phone charger and no video output through it), but there are 3 USB ordinary ports (type A), of which two are 3.1, third is 2.0 (all support only data transfer, no charging). Which means, laptop can’t charge other devices through USB and it can’t be charged through it. There is standard LAN port which is a fine addition (usually missing on newer laptops), a HDMI a/v out (standard size, not mini), usual headphone/mic combo connector and SD card reader. Basics are covered, nothing more, nothing less.

## Battery

One Li-ion 3-cell 41 Wh battery powers the laptop, I thought it would be bad, but given the efficiency of components, the battery life is surprisingly good – depending on usage, one could get the duration of typical working day without topping-up. The charging takes about 2 hours. There is no space for second battery as on some models.

Screen is 15.6” Full-HD (1920*1080) 16:9 anti-glare IPS panel which looks pretty good; the images are reasonably sharp and viewing-angles too. It sports a decent pixel density of 142 ppi. I haven’t tested the gamut and color profiling as I am not into that stuff. It can be a complete desktop-replacement solution even during the longer working times and is pleasant to look at.

## Input devices

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112305.jpg) 

Keyboard is decent, feedback is satisfying given the 2 cm height of the typing board (whole laptop is 2.25 cm but few millimeters go to the top cover which contains the screen). To my satisfaction, the dedicated numerical keyboard is present so you get almost a full-keyboard-alike experience, albeit there are some drawbacks, namely the usual placement of some non-essential keys is displaced all around (ins/del, home/end, pgup and pgdown keys as well as f-keys) – missing IBM/Lenovo classic keyboard. Yet, keyboard is not only satisfying, but actually good – could work long-term on it.

Now the biggest drawback: the touchpad itself and especially buttons are REALLY bad. The touchpad feels somewhat clumsy and not precise enough but is usable; however the touchpad keys are so hard to press that the user must mentally prepare to apply additional force every time the mouse key need to be pressed which ruins whole user-experience BIG TIME. Fast solution would be to simply hook up the external mouse and to avoid touchpad buttons whenever possible.


## Storage

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112305a.jpg) 

The system supports the NVMe SSD and 256 GB unit is installed. The disk benchmark immediately shows very slow transfer rates which indicate an issue of some kind. Apparently, although the disk is PCIE 3.0 x4 capable, the data bus is somehow capped to the only PCIE 1.0 x2, limiting the data transfer to 500 MB/s (which is basically only one-quarter to the max theoretical read and one-third of the write speed). 

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112305b.jpg) 

The BIOS isn’t configurable so the user can’t change anything of importance in it. This is a serious drawback and puts HP on the throne of shame, as the disk transfer rates of such kind are now 15 years old.

Going further, 8 GB of RAM is on the lower edge, especially given the fact that the GPU RAM takes 2 GB of it, so user has only the 6 GB at disposal. For the light, office use, browsing and similar tasks it’ll do the job, however, forget anything more demanding. Expansion to the 16 GB will prove a bit difficult, since one should buy the same memory module which is impossible to find on the market (two same modules would avoid eventual memory issues), so the most probable solution would be acquiring a 2*8 GB modules of DDR4 laptop RAM, which amount to 10% of the laptop’s worth. There is a possibility of replacing SSD as the faster ones are readily available, and, technically, it is possible to add another 2.5” HDD/SSD, however HP complicated things here and didn’t provide the connector which must be manually bought and installed. Since the data rate of NVMe drive is limited to prehistorical values, there is no sense to upgrade it either. 

## Performance

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112305c.jpg) 

As usual, main CPU is soldered and not replaceable so you’re stuck with what you initially obtained in that regard. Even if the RAM is maxed-out, the CPU will probably remain a constraint if heavier software load runs on this machine. I compared the performance of rather old desktop i7 22-nm 3770 CPU with this 3500u Ryzen, both being 4-core, 8-threads, and the i7 just crushes it in terms of everything – it may be not a fair comparison given the different market segments but between those chips is 7 years-difference in favor of that Ryzen – Moore’s Law is stuck in rut that’s for sure. Still no replacement for the displacement.

## Thermals and miscellaneous devices

The machine internal heat management is good – there is no significant heating and the thing stays reasonably cool for a prolonged period of time. The fan runs reasonably quiet and is tolerable even when on full speed.  I have no complaints in terms of peripherals and miscellaneous equipment – speakers, webcam and connectivity work as supposed. Nice thing is that the wireless is 802.11 ac meaning 5 GHz networks are supported.

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112306.jpg) 
![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112307.jpg)

With weight of 1.8 kg (charger excluded), it is somewhat heavier than ultrabooks – but if you want 0,3-0,4 kg reduce in weight with the same specs prepare to pay at least twice as much as this laptop costs! The weight is manageable, it is significantly lower than 2 kg and it is well balanced over the laptop’s area. I measured 1,75 kg without and 2,05 kg with the charger, respectively. The build quality is decent, there are occasional, rare squeaks which is expected given the thin plastic’s body which seems durable. 

![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112308.jpg)
![HP Notebook 15 gen. 2021](https://sbozich.github.io/assets/15112309.jpg)

Portability is average: ideally, you’d want a 14” laptop or less when carrying around, but the additional screen-estate you get with this display is worth if you want portable desktop replacement. It will fit in most backpacks though.

## Conclusion

Although it is a decent machine in most departments, even if it would have had a full-speed NVMe drive I wouldn’t have bought it for the single reason that it does not have USB-C (meaning, you can ditch the original charger and just use your phone’s one to charge it – one thing less in the backpack!). Another thing is crappy touchpad which means that an average user will probably have to carry around external mouse. Third thing, if you are constantly on the move, you’d be better-off with a lower-screened laptop which you can hook-up to larger external screen if needed. If you can live with those things, or if you don’t require much portability, this machine is a decent choice especially if it sits on the desk and doesn’t move often. I am ditching it as it collects dust sitting on the shelf across my mighty reliable desktop PC.
