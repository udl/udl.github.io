---
layout: post
title:  "Cronjobs for alternating weekdays"
date:   2016-11-25 11:39:10
categories: cron, linux
---

I had to create a cronjob for every second and fourth sunday the other
day. Turns out, this is not an easy task. Although cron is really
powerful.

This is the solution:

```
0 4 8-14,22-28 * *    /bin/bash -c 'test $(date +%u) -eq 7 && yourcommand.sh'
```

How does this work? It triggers every day between 8. and 14. as well as
from 22. to 28.

The `test` command checks, if the day of the week is sunday / 7 (see [linux day
formatting](https://www.cyberciti.biz/faq/linux-unix-formatting-dates-for-display/)).

Special thanks go out to
[this](http://serverfault.com/questions/282232/cron-to-run-every-2nd-wednesday) and [this](http://stackoverflow.com/questions/11683387/run-every-2nd-and-4th-saturday-of-the-month) stackoverflow answers.
