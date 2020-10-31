<h1>calamares-archlinux-config</h1>
Configuration to install Arch Linux using Calamares + ArchISO

UNDER CONSTRUCTION! THIS FILE IS NOT FINISHED YET!

<h3>Pre-info</h3>
<ul>
  <li>A <code>$ <i>command</i></code> reffers to the command being run with normal user privileges, a <code># <i>command</i></code> means
  that it should be run as root.</li>
  <li><i>Italic</i> text means that the name can be what you want, but often times you'll have to adjust the config relating to it.</li>
  <li>When you see something like "modules/unpackfs.conf", I am reffering to the modules directory in this repo (or rather the folder you cloned it to).</li>
</ul>

<h3>How to deploy</h3>

0. Log in as root (not technically necessary, but highly recommended to make shure all files are owned by root, to make shure everything
ends up in root's home to prevent permission problems in your own home folder and to save you fram having to type `sudo` or `doas` every time):
<pre>
$ sudo -i
</pre>
or
<pre>
$ doas -s
# cd ~
</pre>
<br>
1. Install all dependendencys:<br><b>Tip</b>: You can also use the `-git` versions from the AUR to get the latest builds.
<pre>
# pacman -S --needed archiso arch-install-scripts
</pre>
<br>
2. Get the archiso base system: 
<pre>
# cp -r /usr/share/archiso/configs/releng ~/<i>archiso</i>
# cd ~/<i>archiso</i>
</pre>
<br>
3. Clone this repo:
<pre>
# mkdir -p ~/<i>archiso</i>/airootfs/etc/calamares
# git clone https://github.com/SkyyySi/calamares-archlinux-config.git ~/<i>archiso</i>/airootfs/etc/calamares
# rm -rf ~/<i>archiso</i>/airootfs/etc/calamares/pkgbuilds
</pre>
<br>
4. Create a squashfs (it has to contain at least the essential packages, but if you want your ISO to work offline, 
add all packages here and remove them from modules/packages.conf):<br><b>Tips</b>:
<ul>
  <li>You can also add you custom files/directories (like your dot files) here.</li>
  <li>You can use custom names and directories here, but you will have to adjust modules/unpackfs.conf acordingly.</li>
  <li>You must include at least the <code>base</code> meta-package, because calamares will try to chroot into the system and run pacman from there.
  It cannot (unlike pacstrap) use the host's pacman installision.</li>
  If you want to make an offline install, you could, replace <code>base</code> with, for example,
  <code>amd-ucode base base-devel efibootmgr grub intel-ucode linux linux-firmware linux-headers nano networkmanager os-prober vim</code>,
  which will give you a minimal, tty (full screen terminal) only system.
</ul>
<pre>
# mkdir -p ~/<i>archiso-squashfs</i>
# pacstrap ~/<i>archiso-squashfs</i> base
# mksquashfs ~/<i>archiso-squashfs</i> ~/</i>archiso-squashfs.sqfs</i>
# mkdir -p ~/<i>archiso</i>/airootfs/usr/share/
# mv ~/<i>archiso-squashfs.sqfs<i> ~/<i>archiso</i>/airootfs/usr/share/<i>archiso-squashfs.sqfs<i>
</pre>

5. Create a package for calamares:<br><b>Tip</b>: You can use the provided PKGBUILD for this, just make shure that, if you use the stable version,
you make shure the version string at the top matches the last version on https://github.com/calamares/calamares/releases

6. Create the ISO:
<pre>
# cd ~/<i>archiso-squashfs</i>
# rm -rf out work   # only needed if you have built it before
# mkarchiso -v -C pacman.conf .
</pre>
You will find your ISO in the out/ directory.
