# SKED Catalogs and EOP Finals Directory

This directory holds the SKED catalogs that VieSched++ downloads. Note that these are typically downloaded from https://github.com/nvi-inc/sked_catalogs.

This directory also holds the EOP finals file in the `9_FINALS.ALL_IAU2000_V2013_019.txt` format as downloaded from https://datacenter.iers.org/data/latestVersion/9_FINALS.ALL_IAU2000_V2013_019.txt.

I recommend downloading these files outside the container and running the container in its default read-only mode as shown below.

First time setup:

```bash
cd catalogs && git clone https://github.com/nvi-inc/sked_catalogs . && curl https://maia.usno.navy.mil/ser7/finals2000A.all > 9_FINALS.ALL_IAU2000_V2013_019.txt
```

Subsequent updates

```bash
cd catalogs && git pull && curl https://maia.usno.navy.mil/ser7/finals2000A.all > 9_FINALS.ALL_IAU2000_V2013_019.txt
```
