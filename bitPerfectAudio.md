# bitPerfectAudio Settings

copied and adapted from this excellent summary [https://m4c.pl/blog/freebsd-audio-setup-bitperfect-equalizer-realtime/#freebsd_hifi_audio]

## /boot/loader.conf

```
sysctlinfo_load="YES" 
mac_priority_load="YES" 
#hint.pcm.1.eq=1
```

The mac_priority(4) module establishes permission scheduling rules so that privileged users can use the rtprio(1) tool to run processes with real-time priority.

##
Add the following lines to your `/etc/sysctl.conf` file:

```conf
# sound setup/bitperfect
hw.snd.default_auto=0
hw.snd.default_unit=0
hw.snd.maxautovchans=0
dev.pcm.0.play.vchans=0
dev.pcm.0.bitperfect=1
hw.snd.latency=0
# dont autoreset vol to default
hw.snd.vpc_autoreset=0
kern.timecounter.alloweddeviation=0
```

** check for bitperfect output **

```
sysctl hw.snd.verbose=2 
cat /dev/sndstat
```


## mpd in real time mode

```
rtprio 0 musicpd /usr/local/etc/musicpd.conf
```

### mpd with ffmpeg filters

``` musicpd.conf
filter {
  plugin "ffmpeg"
  name "equalizer"
  graph "bass=f=62:t=h:w=120:g=10:n=1:r=f64, treble=f=16000:t=h:width=8000:g=2:n=1:r=f64"
}

audio_output {
  type "oss"
  name "OSS"
  mixer_type "hardware"
  replay_gain_handler "none"
  filters "equalizer"
}
```

## cmus
```conf
softvol true
softvol_state 100 100
```
