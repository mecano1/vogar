# Vogar and SSH #
`USB` and `adb` tend to be flaky. You can drive a device with `ssh` instead.

## Install an SSH server on the device ##
Install [SSHDroid](https://market.android.com/details?id=berserker.android.apps.sshdroid), a simple on-device ssh server.

## Configure passwordless authentication ##
Set up passwordless SSH access using [ssh-keygen](http://rcsg-gsir.imsb-dsgi.nrc-cnrc.gc.ca/documents/internet/node31.html). Transfer the authorized key to the device using `adb` and import that into SSHDroid.

## Configure SSH control master ##
SSH key exchange takes several seconds. Since vogar makes many SSH connections, this is prohibitively slow. We can avoid the SSH key exchange penalty by sharing one SSH session among all vogar commands.

Enable SSH control master in your `.ssh/config` file:
```
$ cat ~/.ssh/config 
Host *
  ControlMaster auto
  ControlPath ~/.ssh/master-%r@%h:%p
```

## Create control master connection ##
Establish the control master SSH session:
```
$ ssh -p 2222 192.168.144.89
```
Leave this session open indefinitely. As long as this session is alive,  SSH connections to that host will be fast.

## Run vogar with ssh ##
Vogar's `--ssh` option expects a hostname and port separated by a colon. It will use `ssh` instead of `adb` to communicate with the device.
```
$ vogar --ssh 192.168.144.89:2222 --mode app_process MyTest.java 
```