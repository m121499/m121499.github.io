---
name: Active Directory Attack and Defense
tools: [Active Directory, Kali Linux, LAMP]
image: ../images/microsoft-active-directory.jpg
description: An Active Directory home lab I set up to learn about various technologies
#external_url: ../new.html
---

# About

This project covers my extensive Active Directory lab. I created this lab to learn about administering/setting up an Active Directory Environment, setting up a LAMP server using Ubuntu, and to practice performing/defending Active Directory attacks. I originally created the lab to improve my Active Directory administration skills. After refining my active directory knowledge I wanted to learn how to build a fully interactive LAMP web server. In my class we built a basic LAMP server that stored some basic information such as name, favorite color etc... I used the knowledge learned from that lab as well as some online resources to create a login portal. I created a simple login portal. I then utilized an Active Directory Certificate Server to trust my web server. After performing all those configurations I wanted to work on my offensive security skills. So I disabled windows defender and the attacking began. Rather than just attacking, I wanted to practice by first making a service vulnerable, hacking it, then applying the appropriate remediation. Note that the attached demonstrations are not meant to be walkthroughs, they are meant to illustrate the skills I possess and some challenges I faced along the way! For brevity, not every step is included such as the DHCP, DNS, and most other Active Directory Steps


{% include elements/button.html link="../pages/ad1.html" text="Users and permissions" %}
{% include elements/button.html link="../pages/attacking.html" text="Attacking Active Directory" %}
{% include elements/button.html link="../pages/defending.html" text="Defending Active Directory" %}
{% include elements/button.html link="../pages/webserver.html" text="Web Server Part 1" %}
{% include elements/button.html link="../pages/webserver2.html" text="Web Server Part 2" %}
