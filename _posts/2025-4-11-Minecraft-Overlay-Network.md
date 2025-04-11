---
layout: post
title:  "How Tailscale Turned My Minecraft Server into a Secret Society"
date:   2025-4-11
categories: jekyll update
---

### Hosting a Public Mincraft Server 

As tradition dictates, our friend group entered the sacred two-week Minecraft phase. You know the one—where everyone suddenly agrees to drop all responsibilities and go full survival mode until the server dies of neglect.

But before any blocks are punched, we had to make two critical decisions:

- What mods (if any) are we adding?
- Who’s hosting the server?

Due to the groups inability to agree on a combination of mods, we keep things simple—just classic, base vanilla Minecraft. So we googled “best free Minecraft hosting”. Settled on Minehut.gg, tossed them $10 to beg their AWS VM's for the bare minimum amount of compute, and got ourselves a shiny new IP and domain.

I fired up Minecraft → Multiplayer → Add Server → pasted the domain → DNS did its thing → boom, I was in. I picked a snowy biome and built myself a cozy little cabin. All was well in the realm.

…until Day 5.

I log in, and there’s a stranger on the server. I didn't recognize the name. At first, he seemed harmless. But within minutes, the mystery guest turned into a spawn-killing menace, repeatedly harassing me terorizing the server.

It was chaos. I got so frustrated I logged off.

Yes, I could have explored Minehut’s configuration settings, enabled a whitelist, or locked it down better—but hey, hindsight is 20/20.

### What is Tailscale 

I actually stumbled across Tailscale by chance. Our rockstar Senior Engineer was walking us through the ridiculous amount of setup it took to secure a public server—from configuring middleware to layering on Cloudflare for DDoS protection—just so he could access his OctoPrint dashboard remotely. Funny enough, our Data Scientist casually chimed in, “Have you heard of this thing called Tailscale? It does all of that... out of the box.” We were so curious we had to explore it for ourselves. 

If you want to know about tailscale, you can read the official [blog](https://tailscale.com/kb/1151/what-is-tailscale). 
TL;DR: Tailscale is a zero-config VPN that creates a secure private network between your devices. It handles NAT traversal (no more port forwarding!) and lets you access devices from anywhere using human-readable names like laptop.tailnet-name.ts.net. If both devices are online and on the same tailnet, you can reach them—and any services they host—just by name.


### You see where this is going 

Instead of relying on Minehut, I realized I could just host the Minecraft server directly on my own machine. I used the [Minecraft Docker image](https://hub.docker.com/r/itzg/minecraft-server) so the server could run in the background 24/7. Since Tailscale supports MagicDNS, any device on my Tailnet can resolve machine names automatically. With my Docker container running on port 25565—and Minecraft defaulting to that port—anyone on my Tailnet just needs to type in my machine’s name, or check the admin console for its IP, and they’re in! 

Security’s taken care of too—only devices on the Tailnet can access the server. It’s completely isolated from the public internet and only reachable through our private VPN. If someone decides to cause chaos, the admin can easily kick them by removing their access via the Tailscale admin console. Re-adding them later is just as easy.




