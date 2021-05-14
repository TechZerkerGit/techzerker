---
title: "Office 365: Web Office Apps & Network Shares"
subtitle: "Small Challenges Become Big Merging On-Prem and Cloud"
date: 2021-05-14
draft: true
# images: []
tags: ["Windows","Microsoft",”Office 365”,”Cloud”]
categories: ["Windows","Career IT"]
resources:
#- name: featured-image
#  src: bmwtest.jpg
#- name: featured-image-preview
#  src: bmwtest.jpg
---

The nature of working in the technology infrastructure and support field, is one of small (or misunderstood) problems turning into large scale change. It is no different than the developers world of a *simple* change to the end user requiring major code refactoring or design change.

The problem on display this week started as:
> *How to E1 License Web App only users access files on local network shares (file server) easily?*
<!--more-->

The available solutions after reading, trial and error are limited. The root cause is native Windows 10 in its current state, with no local Office apps installed, does not play with the web apps like *Word Online*. Meaning, if a regular user does what they’ve always done, and navigates to a *word.docx* file and double clicks... they are presented with an unfriendly *please choose an application to open this or browse the store* prompt.

![Find App for File Extension Error](image.image)

