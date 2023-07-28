I have an LPR-3000R, which supports remote Ethernet-based RF span and magnitude captures. However, I haven't been able to get any useful responses from the two open TCP/IP ports.

## Ethernet Port

A port scan identifies the following open ports:

```
631/tcp   open     ipp            CUPS 1.1
| http-methods: 
|   Supported Methods: GET HEAD OPTIONS POST PUT
|_  Potentially risky methods: PUT
|_http-title: 403 Forbidden
|_http-server-header: CUPS/1.1
```

This port returns 403 Forbidden for every URI I've tried.

Port 5000 is also open, but does not respond to any SCPI commands. It closes the connection after a few seconds, regardless of what is typed.

I was able to find an installation manual for this piece of equipment on the Internet, which indicates that port 5000 is used by a LabView-based program that communicates via NI-VISA.

Trying to connect using NI VISA doesn't help. It's not using VXI-11, and when using socket mode, it does not respond to identify, etc.

The vendor's program must be sending some special initiation sequence before it will start responding to SCPI commands.

## Serial Port

Connecting the serial port and pressing "#" lets me enter "Terminal Mode". This interface allows me to use some SCPI commends. As I was unable to find any documentation for the SCPI interface for the instrument, here are the commands that I was able to figure out:


### Device Identify

Returns the vendor, device and version.

```
> *idn?
LP Tech,LPT-3000A,123335,1.1.44.7.3
```


### System Time

Returns the time in hours,minutes,seconds (24 hour time).

```
> :SYST:TIME?
21,36,51
```

Setting the time also works:

```
> :SYST:TIME 16,30,30
> :SYST:TIME?
16,30,34
```

### System Date

Returns the date in YYYY,M|MM,D|DD.

```
> :SYST:DATE?
2012,4,9
```

Setting date time also works, however, it requires a leading zero for single-digit months/days:

```
> :SYST:DATE 2023,07,27
> :SYST:DATE?
2023,7,27
```

### Frequency Span

The frequency CENTer, SPAN, STARt and STOP commands allow the range to be swept to be displayed and configured. GHz, MHz and kHz values are supported as inputs.

```
> :FREQ:CENT?
1.500000000 GHz
> :FREQ:SPAN?
3.000000000 GHz
> :FREQ:STAR?
0 Hz
> :FREQ:STOP?
3.000000000 GHz
```

Changing values:

```
> :FREQ:CENT 123.456 MHz
> :FREQ:CENT?
123.456000 MHz
> :FREQ:STOP 222.222 MHz
> :FREQ:STOP?
222.222000 MHz
> :FREQ:STAR 111.111 MHz
> :FREQ:STAR?
111.111000 MHz
> :FREQ:SPAN?
111.111000 MHz
```

### Video Bandwidth

This sets the video bandwidth used. Not every value seems to be supported (for example, requesting it be set to 2 MHz sets it to 1 MHz).

:BAND and :BAND:VID seem to be synonyms.

```
> :BAND:VID?
3.000000 MHz
> :BAND 2MHZ
> :band:vid?
1.000000 MHz
> :band?
1.000000 MHz
```

The video bandwidth can also be set to auto:

```
> :BAND:AUTO? 
OFF
> :BAND:AUTO ON
> :BAND:AUTO?
ON
> :BAND?
1.000000 MHz
```

### Sweep Time

The sweep time can be manually controlled, or set to AUTO. ms and s inputs are supported.

```
> :SWEEP:TIME?
143 ms
> :SWEEP:TIME 200 s
> :SWEEP:TIME?
200.000 s
> :SWEEP:TIME:AUTO?
OFF
> :SWEEP:TIME:AUTO ON
> :SWEEP:TIME?
143 ms
```

### Markers

Markers allow you to read back measured values at a specific frequency. Setting an "x" value (frequency) for a marker sets the mode to "normal".

```
> :CALC:MARK1:MODE?
OFF
> :CALC:MARK1:x?
0 Hz
> :CALC:MARK1:y?
-999.99 dBm
> :CALC:MARK1:x 1MHz
> :CALC:MARK1:x?
111.110000 MHz
> :CALC:MARK1:y?
-44.18 dBm
> :CALC:MARK1:MODE?
NORMAL
```

Four markers are supported.

I have not experimented with the SCPI commands to find the highest value to the right or left of the marker.

### Traces

Traces allow you to get a series of values corresponding to a frequency sweep.

```
> :TRACE1:MODE?
WRITE
> :trace:data? TRACE1
-44.32 dBm,-46.14 dBm,-41.61 dBm,-42.21 dBm,-45.83 dBm,-48.65 dBm,-46.56 dBm,-44.71 dBm,-44.35 dBm,-44.24 dBm,-41.70 dBm,-45.75 dBm,-42.51 dBm,-46.68 dBm,-47.06 dBm,-44.53 dBm,-42.22 dBm,-45.70 dBm,-45.71 dBm,-45.90 dBm,-47.16 dBm,-46.40 dBm,-44.70 dBm,-44.43 dBm,-47.11 dBm,-42.56 dBm,-47.46 dBm,-45.37 dBm,-42.02 dBm,-44.37 dBm,-45.57 dBm,-46.03 dBm,-42.83 dBm,-46.08 dBm,-48.60 dBm,-43.77 dBm,-43.45 dBm,-45.70 dBm,-45.62 dBm,-46.74 dBm,-44.49 dBm,-48.11 dBm,-49.00 dBm,-44.76 dBm,-44.81 dBm,-43.01 dBm,-49.81 dBm,-46.01 dBm,-44.26 dBm,-45.62 dBm,-46.50 dBm,-49.48 dBm,-47.18 dBm,-44.50 dBm,-44.13 dBm,-48.92 dBm,-49.98 dBm,-48.17 dBm,-43.85 dBm,-45.06 dBm,-46.26 dBm,-46.50 dBm,-48.30 dBm,-45.44 dBm,-51.52 dBm,-46.47 dBm,-45.75 dBm,-43.04 dBm,-46.93 dBm,-43.65 dBm,-43.65 dBm,-46.43 dBm,-47.12 dBm,-44.11 dBm,-46.36 dBm,-45.53 dBm,-44.88 dBm,-49.30 dBm,-45.56 dBm,-44.10 dBm,-43.09 dBm,-47.04 dBm,-43.67 dBm,-43.42 dBm,-45.19 dBm,-46.62 dBm,-44.23 dBm,-44.14 dBm,-47.62 dBm,-45.36 dBm,-41.49 dBm,-45.97 dBm,-49.22 dBm,-46.58 dBm,-48.13 dBm,-43.60 dBm,-44.65 dBm,-45.85 dBm,-44.26 dBm,-44.88 dBm,-44.17 dBm,-47.90 dBm,-48.80 dBm,-45.66 dBm,-45.74 dBm,-46.93 dBm,-46.31 dBm,-45.34 dBm,-45.98 dBm,-43.50 dBm,-43.47 dBm,-45.24 dBm,-43.07 dBm,-44.30 dBm,-44.93 dBm,-42.43 dBm,-48.48 dBm,-45.19 dBm,-42.62 dBm,-46.28 dBm,-46.12 dBm,-48.52 dBm,-44.24 dBm,-49.14 dBm,-46.08 dBm,-44.10 dBm,-48.66 dBm,-42.17 dBm,-45.00 dBm,-48.22 dBm,-45.76 dBm,-39.58 dBm,-46.53 dBm,-46.38 dBm,-45.52 dBm,-44.89 dBm,-44.73 dBm,-49.19 dBm,-45.47 dBm,-43.37 dBm,-47.79 dBm,-43.79 dBm,-43.59 dBm,-43.81 dBm,-43.75 dBm,-47.44 dBm,-45.93 dBm,-45.10 dBm,-43.24 dBm,-45.59 dBm,-46.87 dBm,-45.41 dBm,-47.29 dBm,-43.79 dBm,-44.42 dBm,-45.66 dBm,-45.55 dBm,-45.95 dBm,-43.58 dBm,-45.61 dBm,-47.49 dBm,-46.27 dBm,-47.67 dBm,-45.55 dBm,-48.23 dBm,-48.26 dBm,-45.11 dBm,-49.12 dBm,-46.55 dBm,-46.71 dBm,-46.08 dBm,-48.30 dBm,-46.44 dBm,-46.01 dBm,-46.32 dBm,-45.98 dBm,-44.52 dBm,-47.02 dBm,-44.71 dBm,-48.49 dBm,-47.11 dBm,-44.68 dBm,-43.96 dBm,-49.16 dBm,-45.75 dBm,-45.18 dBm,-47.41 dBm,-47.26 dBm,-44.06 dBm,-43.27 dBm,-48.09 dBm,-42.80 dBm,-43.53 dBm,-43.52 dBm,-44.05 dBm,-45.55 dBm,-43.42 dBm,-45.85 dBm,-45.76 dBm,-47.73 dBm,-50.45 dBm,-44.73 dBm,-47.64 dBm,-45.10 dBm,-43.16 dBm,-47.24 dBm,-45.96 dBm,-46.59 dBm,-46.74 dBm,-43.95 dBm,-41.95 dBm,-47.32 dBm,-49.02 dBm,-42.91 dBm,-48.19 dBm,-45.40 dBm,-47.53 dBm,-46.97 dBm,-45.65 dBm,-46.78 dBm,-46.43 dBm,-45.27 dBm,-45.74 dBm,-44.73 dBm,-46.98 dBm,-42.97 dBm,-46.66 dBm,-44.89 dBm,-44.81 dBm,-45.58 dBm,-48.46 dBm,-46.04 dBm,-46.30 dBm,-47.26 dBm,-42.96 dBm,-47.05 dBm,-42.72 dBm,-47.28 dBm,-49.01 dBm,-48.42 dBm,-43.94 dBm,-45.80 dBm,-41.83 dBm,-43.67 dBm,-44.27 dBm,-45.95 dBm,-46.69 dBm,-43.38 dBm,-45.81 dBm,-43.48 dBm,-45.49 dBm,-45.95 dBm,-47.96 dBm,-47.37 dBm,-44.54 dBm,-42.68 dBm,-47.12 dBm,-44.25 dBm,-47.80 dBm,-41.55 dBm,-46.07 dBm,-45.55 dBm,-48.26 dBm,-48.82 dBm,-45.73 dBm,-46.92 dBm,-44.67 dBm,-45.07 dBm,-47.31 dBm,-46.04 dBm,-45.43 dBm,-42.16 dBm,-45.34 dBm,-47.54 dBm,-47.34 dBm,-43.33 dBm,-47.28 dBm,-44.41 dBm,-48.20 dBm,-47.48 dBm,-40.83 dBm,-46.19 dBm,-44.88 dBm,-46.07 dBm,-45.76 dBm,-45.88 dBm,-45.05 dBm,-46.12 dBm,-49.26 dBm,-50.86 dBm,-49.95 dBm,-45.76 dBm,-41.82 dBm,-46.80 dBm,-47.09 dBm,-49.62 dBm,-46.16 dBm,-42.50 dBm,-49.62 dBm,-43.41 dBm,-45.18 dBm,-47.48 dBm,-46.59 dBm,-44.04 dBm,-45.61 dBm,-43.64 dBm,-45.42 dBm,-47.25 dBm,-43.11 dBm,-44.78 dBm,-45.91 dBm,-46.19 dBm,-44.85 dBm,-47.16 dBm,-46.87 dBm,-48.69 dBm,-48.34 dBm,-49.97 dBm,-44.91 dBm,-48.79 dBm,-44.93 dBm,-46.32 dBm,-45.44 dBm,-45.61 dBm,-44.35 dBm,-46.51 dBm,-46.70 dBm,-44.89 dBm,-50.08 dBm,-46.60 dBm,-46.99 dBm,-46.64 dBm,-43.12 dBm,-43.72 dBm,-46.02 dBm,-43.20 dBm,-46.91 dBm,-45.90 dBm,-47.61 dBm,-47.50 dBm,-46.43 dBm,-46.48 dBm,-44.37 dBm,-44.78 dBm,-44.09 dBm,-49.38 dBm,-44.64 dBm,-43.49 dBm,-47.92 dBm,-42.70 dBm,-43.43 dBm,-43.53 dBm,-43.55 dBm,-43.01 dBm,-45.85 dBm,-46.04 dBm,-48.10 dBm,-46.70 dBm,-42.99 dBm,-45.45 dBm,-44.52 dBm,-42.27 dBm,-46.57 dBm,-50.20 dBm,-46.45 dBm,-44.71 dBm,-45.80 dBm,-46.09 dBm,-43.47 dBm,-44.17 dBm,-45.07 dBm,-51.04 dBm,-46.61 dBm,-41.80 dBm,-42.61 dBm,-50.55 dBm,-45.12 dBm,-48.04 dBm,-44.46 dBm,-45.06 dBm,-45.26 dBm,-43.01 dBm,-46.35 dBm,-43.01 dBm,-47.03 dBm,-47.42 dBm,-44.53 dBm,-48.71 dBm,-46.46 dBm,-45.95 dBm,-45.06 dBm,-48.12 dBm,-50.66 dBm,-44.46 dBm,-44.71 dBm,-43.89 dBm,-48.55 dBm,-47.68 dBm,-43.82 dBm,-48.06 dBm,-46.52 dBm,
```

Here's an example of what this looks like when plotted:

![image](https://github.com/dslik/kb-notes/assets/5757591/b44af82f-8273-48b1-9dd1-63748245ddcb)

Changing the sweep time or the video bandwidth does not alter the number of data points returned. Nor does `:trace:data:points?` return the number of points.

Changing the mode of a Trace to "VIEW" stops it from updating.

```
> :TRACE1:MODE VIEW
> :TRACE1:MODE?
VIEW
```

## Attenuator

The version of the LPT-3000R that I have has a built-in attenuator. This can be controlled using the ATT commands:

```
> :POW:ATT?
30 dB
> :POW:ATT 20db
> :POW:ATT?
20 dB
> :POW:ATT:AUTO?
OFF
> :POW:ATT:AUTO ON
> :POW:ATT:AUTO?
ON
> :POW:ATT?
20 dB
```

## Misc

Here are a few commands I haven't dug into yet:

```
> :INIT:CONT?
ON
```

This displays the measured units. I have not been able to change them to anything else.

```
> :UNIT:POW?
DBM
```

