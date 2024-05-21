---
title: '{{ replace .File.BaseFileName "-" " " | humanize | title }}'
date: {{ .Date | time.Format "2006-01-02" }}
draft: true
authors:
  - name: Akruti Ambade
    link: https://github.com/ackruti
    image: https://github.com/ackruti.png
summary: Add your post summary here.
tags:
  - tag1
  - tag2
---