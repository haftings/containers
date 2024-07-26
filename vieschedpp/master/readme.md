# VLBI Master Schedule Directory

This directory holds the master schedules that VieSched++ downloads.

IVS master files are available from any of the [IVS data centers](https://ivscc.gsfc.nasa.gov/products-data/data.html) in the `ivscontrol` directory.

For non-authenticated users or users wanting to duplicate VieSched++'s actions at time of writing, you can access files by FTP from OPAR:

```bash
cd master && wget -N --accept .txt --reject notes.txt ftp://ivsopar.obspm.fr/vlbi/ivscontrol/master*.txt
```
