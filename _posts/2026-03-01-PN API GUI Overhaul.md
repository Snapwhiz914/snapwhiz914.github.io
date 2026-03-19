---
layout: post
title:  "GUI Overhaul for PN-API"
date:   2026-03-18 12:00:00 -0400
author: Snapwhiz914
tags: proxy website fastapi
---

When I started this blog about a year and a half ago, one of my first posts was for [PN-API](/2024/08/12/PN-API), a service that scans IP addresses on proxy lists to determine if they are alive, geolocation and anonymity information if they are alive, and presents them as a .PAC file, an API and a simple webpage with a map. I didn't touch it since I wrote the initial post until over winter break when I started experimenting with Github Copilot.

Over the break I had Github Copilot (the free version) completely overhaul the , replacing the simple one-page leaflet map with a proper react frontend. I was so impressed with just how quickly I could implement features with Github Copilot that I spent the next few days implementing other features that I had wanted for a while, including users, profiles and ping history which is used to calculate a reliability score for a certain proxy.

This post will be a presentation of the new website as well as new features.

# New features

## An actual website

The frontend is now a single page react app. I chose to have Copilot use this because I know OpenAI's ChatGPT uses this frontend stack and I like the style.

### Login
![Login](/assets/images/2026-03-01-PN-API-GUI-Overhaul/new-login-small.png)
### Home
![Home](/assets/images/2026-03-01-PN-API-GUI-Overhaul/home.png)

There is no option for a light theme and I would have to be well convinced to add one - the Mantine dark theme looks very sleek and serious in my opinion. I did briefly consider changing the theme of this blog to match it, but the blueish dark background that I have now has a little bit more character.

The navigation being a set of buttons to the right of the page title is definitely unusually. When I prompted Copilot originally I implied that I wanted a top tab navigation but I don't think it got that and improvised. I don't dislike it but right now it is inconsistent across different pages which will need to be fixed.

## New Map

What once was one page with a leaflet map and busy-looking filtering options is now a sleek, react frontend (still using leaflet) with better looking filters.

### Before
![Old one-page website](/assets/images/2024-08-12-PN-API/Screenshot.png)
### After
![New website](/assets/images/2026-03-01-PN-API-GUI-Overhaul/new-map.png)

As crammed as they may have been, I do like having all of the filtering features in one row at the top of the screen. The new layout has lots of padding between filter controls (and within them) which I will likely end up changing.

## Profiles!

This feature was in the very first plan for PN when I wrote it in eighth grade (2021, so long ago), but it got pushed so far back on the priority list that I forgot about originally.

Profiles are a saved set of filters that you can generate a [.PAC](https://en.wikipedia.org/wiki/Proxy_auto-config) script from. Every time the PAC script is fetched from the server, it filters the currently alive proxies based on the profile filters, and injects the resulting proxy IPs into the PAC script template.

![Filtering dialog](/assets/images\2026-03-01-PN-API-GUI-Overhaul/edit-filter.png)

## Scan history

Since the first working iteration of this project, I've wondered what the uptime history of these proxies look like. This overhaul saves every ping to a proxy to the MongoDB database as a HistoricalPing object. The HistoricalPing object stores scan time, if the proxy responded, and if it responded, the server headers and the response speed. The HistoricalPing collection for each URI is used in calculating proxy reliability.

On the frontend, there is now an analysis page where you can plot proxy speed over time. I'm still working on this page, and I'm excited to see the insights into proxy performance I can get from it.

![Scan history](/assets/images\2026-03-01-PN-API-GUI-Overhaul/scan-history.png)

## Scanner settings & statistics

I added some extra settings to the scanner:
- You can now specify specific websites that you want each proxy to be able to connect to for it to be considered alive
- You can add and remove blacklist files while the server is running

Finally, there is a frontend page for editing these settings during runtime as well as viewing scanner statistics.

![Scan history](/assets/images\2026-03-01-PN-API-GUI-Overhaul/scanner-settings.png)

# Future

There is still a lot of potential with this project. A few features I have in mind:
1. **A proxy chain creator**: either you choose what specific proxies you want to chain together in what order, or you define which proxies should be chosen, to create a proxy chain configuration file that can be used for [proxychains-ng](https://github.com/rofl0r/proxychains-ng). Ideally the client would have a refresh script that runs every 5 minutes that fetches the updated config file from an API endpoint and updates the proxychains-ng daemon.
2. **More protocols**: I recently discovered [Project V](https://github.com/v2fly/v2ray-core), a collection of modern proxy protocols. Judging by the amount of github repositories hosting free configuration files for v2ray clients that are filled with routing nodes IPs, as well as the already existing [Python support](https://pypi.org/project/v2ray2proxy/), I think this is worth looking into.
3. **Desktop client**: I'm open to creating a desktop client, but it's far away on the priority list. The only reason I can see this being helpful right now is local proxy management, eg. ensuring that the PAC script is updated or managing proxychains-ng config.

As for the next update: I don't know. To be honest, I only really work on this project when something else that I'm doing needs its functionality. So if I need one of these future features later down the line, you'll be seeing another post then.