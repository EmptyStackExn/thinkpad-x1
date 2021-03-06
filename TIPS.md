Add More Status Icons
----------------------

![A screenshot of my GNOME Indicators](img/gnome-indicators.png)

They make my day! In GNOME Panel, status icons can be found in three kinds

1. Applets unifed in `Indicator Applet Complete`
2. Notification Icons in `Notification Area`
3. Seperated Panel Items (less common)

GNOME already provides several very interesting status icons. Here are some additional, I would highly advise

* [Redshift](http://jonls.dk/redshift/) - adjusts the color temperature of your screen according to your surroundings
* [WildGuppy](http://www.thecutestgeek.com/wildguppy) - adjusts a laptop's brightness automatically
* [Telegram Desktop](https://desktop.telegram.org/) - cloud-based mobile and desktop messaging app with a focus on security and speed
* [SpiderOak](https://spideroak.com/) - share, sync, and back up data without compromising privacy
* [Desktop Notifications for Android](http://projects.hcilab.org/notification/) - receive Android notifications in your browser (continuously running Google Chrome in background)
* [Xpad](https://launchpad.net/xpad) - a sticky notes application
* [Hardware Sensors Indicator](https://launchpad.net/indicator-sensors) - showing hardware sensors
* [Recent Notifications](https://launchpad.net/~jconti/+archive/ubuntu/recent-notifications) - collects recent messages sent with `libnotify`
* [Pastie](http://github.com/fmoralesc/pastie/) - a simple *nix clipboard manager
* [Feedindicator](https://code.google.com/p/feedindicator/) - RSS feed updates
* [Radio Tray](http://radiotray.sourceforge.net/) - online radio streaming player
* [ClassicMenu Indicaotr](http://www.florian-diesch.de/software/classicmenu-indicator/) - a classic GNOME-style application menu
* [Byzanz](https://github.com/GNOME/byzanz) - record a running X desktop to an animation suitable for presentation in a web browser

After installing, you can configure your system to launch them at startup by either adding lines to `Startup Application Preferences` or adding `.desktop` shortcuts in `~/.config/autostart/`.

----------------------

Upgrade hardware components
----------------------
  * Avoid swapping, upgrade RAM to 8GB: [Corsair® CMSO8GX3M1A1333C9 (8GB DDR3 SODIMM Memory)](http://www.corsair.com/en-us/value-select-8gb-ddr3-sodimm-memory-kit-cmso8gx3m1a1333c9)
  * Delay regular hard-drive failure, use a SSD: [Seagate® 600 SSD ST480HM000](http://www.seagate.com/www-content/product-content/seagate-laptop-fam/600-ssd/fr/docs/600-ssd-data-sheet-ds1780-1-1304fr.pdf)

----------------------

Plug your [Android device](http://www.android.com/) with [MTP](http://www.androidcentral.com/ics-feature-mtp-what-it-why-use-it-and-how-set-it) protocol
----------------------
You can plug your device and automatically get it mounted without performing any commands on 12.04 with a [GVFS](http://library.gnome.org/misc/release-notes/2.22/#sect:gvfs-gio) MTP backend provided by [langdalepl](https://launchpad.net/~langdalepl/+archive/ubuntu/gvfs-mtp) as well as newer versions of Ubuntu.

 1. Add the following PPA repository: `sudo add-apt-repository ppa:langdalepl/gvfs-mtp`
 2. Update your packages: `sudo apt-get update && sudo apt-get upgrade`

----------------------

Remap inconvient keys (`CapsLock` for instance...)
----------------------

Change `CapsLock` into `Menu` and `Squared` into `CapsLock`. Put the following in `~/.Xmodmap`

	keycode 66 = Menu
	clear Lock
	keycode 49 = Caps_Lock

----------------------

Encrypt your Hard Drive ([howtogeek.com](http://www.howtogeek.com/116032/how-to-encrypt-your-home-folder-after-installing-ubuntu/))
----------------------
Research centers and governmental agencies [recommend research data encryption](https://aresu.dsi.cnrs.fr/spip.php?rubrique99) to avoid sensitive information leakage. For this reason, [eCryptfs](http://ecryptfs.org/) made it easy and may not be greedy as the [Intel® Core™ i5-2520M Processor](http://ark.intel.com/fr/products/52229/Intel-Core-i5-2520M-Processor-3M-Cache-up-to-3_20-GHz) provides hardware [Intel® AES-NI](http://www.intel.com/content/www/us/en/architecture-and-technology/advanced-encryption-standard--aes-/data-protection-aes-general-technology.html?_ga=1.149398710.168035845.1418680010) support.

 1. Install required tools: `sudo apt-get install ecryptfs-utils cryptsetup`
 2. Create a temporary user with `Administrator` rights
 3. Log on it and run `sudo ecryptfs-migrate-home -u [user]`
 4. Encrypt the swap partition: `sudo ecryptfs-setup-swap`

If you are using fingerprint authentication, you will notice this will not be anymore possible at desktop manager/logon (e.g. `lightdm`). You remove the fingerprint prompt at that time by

 1. Creating a new PAM configuration file: `sudo cp /etc/pam.d/common-auth /etc/pam.d/common-auth-nofinger`
 2. Comment in the former the following line: `auth [success=3 default=ignore] pam_fprintd.so`
 3. Change in `/etc/pam.d/lightdm` the `@include` directive to redirect from now on to `/etc/pam.d/common-auth-nofinger`

----------------------

Monitor and Gauge Hard Drive Failure
----------------------
Monitoring your hard drive health allows you [to gauge (and eventually to anticipate) disk failure to avoid data loss](http://www.linuxjournal.com/content/know-when-your-drives-are-failing-smartd). This information is computed and reported by the disk thanks to the [SMART](http://wdc.custhelp.com/app/answers/detail/a_id/251/) technology. 

  1. Install the latest vrsion of [`smartmontools`](http://www.smartmontools.org/)
  2. Check if your disk is SMART capable: `sudo smartctl -i [disk path]"`
  3. Check disk general state of health: `sudo smartctl -H [disk path]`
  4. Update drive database: `sudo update-smart-drivedb`
  5. Configure the `smartd` daemon
  
  - Start daemon on system startup by uncommenting or adding the following lines in `/etc/default/smartmontools`:

		start_smartd=yes
		smartd_opts="--interval=1800"

  - Set daemon to monitor all devices by uncommenting/adding the following line in `/etc/smartd.conf`:

		DEVICESCAN -d removable -n standby -m root -M exec /usr/share/smartmontools/smartd-runner

  - Check log entries in `/var/log/syslog` for `smartd` logs
  - Check if `smartd` is running: `service smartd status`. If not, solve why System V init cannot run it at bootup ([UbuntuBootupHowto](https://help.ubuntu.com/community/UbuntuBootupHowto))
  - You can also manually start or stop the daemon: `sudo /usr/local/etc/init.d/smartd [start | stop]`
  - If you use RAID mirroring, you may need to add [`ARRAY`](http://www.ibiblio.org/elemental/howto/disk-monitoring.html)


----------------------


Improve endurance and performance of your SSD with [TRIM](http://www.intel.com/support/ssdc/hpssd/sb/CS-031242.htm?wapkw=%28TRIM%29)
----------------------
  1. Check for TRIM support: `sudo hdparm -I /dev/sda | grep "TRIM supported"`
  2. Add to `/etc/cron.daily/trim` the following lines and make it executable with `chmod +x ...`:
```
#!/bin/sh
LOG=/var/log/trim.log
echo "*** $(date -R) ***" >> $LOG
fstrim -v / >> $LOG
fstrim -v /home >> $LOG
```

----------------------

Manually control fan speed
----------------------

Fan noise can be very aggressive. You can manually set speed with `thinkfan`.
  - http://overtag.dk/wordpress/2012/03/to-all-my-thinkpad-friends-running-linux/
  - http://29a.ch/2011/10/14/review:-ubuntu-linux-on-thinkpad-x1
  - https://askubuntu.com/questions/22108/how-to-control-fan-speed

----------------------


Fix sound in a OS X Mavericks virtual machine in VMware Workstation 10
----------------------
  1. Identify the audio output used by the host system with `aplay -L`. It may look like `sysdefault:CARD=PCH`.
  2. Open the virtual machine `.vmx` file and change the following variables with the identified device

		sound.fileName = "sysdefault:CARD=PCH"
		sound.autodetect = "FALSE"

----------------------

Logon with fingerprint
----------------------
  - tutorial for X1: http://fcns.eu/2012/04/29/fingerprint-reader/
  - http://www.ullrich-online.cc/fingerprint

----------------------


Use Flatten Design
----------------------
  - flatten icons made by NitruxSA for GNOME: https://github.com/NitruxSA/flattr-icons
  - window decoration with [Compiz/Emerald](http://wiki.compiz.org/Decorators/Emerald): http://gnome-look.org/content/show.php/Elegant+Brit?content=74553
  - some discussions: http://bits.blogs.nytimes.com/2013/04/23/the-flattening-of-design/?_r=0

----------------------

Get the `Menu` key back
----------------------
  - http://efod.se/writings/linuxbook/html/caps-lock-to-ctrl.html

----------------------

Type with IBus in Vietnamese (quốc ngữ) Telex
----------------------
  1. Install [IBus](https://code.google.com/p/ibus) : `sudo apt-get install ibus ibus-m17n im-config`
  2. Set up input method to `ibus` with `im-config`
  3. Add and use `vietnamese - telex (m17n)`

----------------------

Get rid of Internal System Error Apport Popups
----------------------
  1. In `etc/default/apport`, change `enabled` from `1` to `0`
  2. Stop Apport process: `sudo service apport stop`

----------------------


Misc
----------------------
  * See emoji icons with `ttf-ancient-fonts` package


Set a better display color profile
----------------------
  * Use the [ICC color profile](http://www.notebookcheck.net/uploads/tx_nbc2/Lenovo_ThinkPad_X1_1366x768_glare_LP133WH2-TLM5.icc) made by X-Rite i1Display 2 on [NotebookCheck](http://www.notebookcheck.net/Review-Lenovo-ThinkPad-X1-Subnotebook.55370.0.html)

