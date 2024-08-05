# VMware CLI Commands

## Enable Maintenance Mode

``` shell
[juan@fractal:~] esxcli system maintenanceMode get
Disabled
[juan@fractal:~] esxcli system maintenanceMode set --enable=true
[juan@fractal:~] esxcli system maintenanceMode get
Enabled
```

## List Profiles

``` shell
[juan@fractal:~] esxcli software sources profile list -d /vmfs/volumes/fractal_hdd/VMware-ESXi-7.0U3g-20328353-depot.zip
.ash_history      bin/              bootpart4kn.gz    lib/              locker/           proc/             scratch/          tardisks/         var/              vmupgrade
.mtoolsrc         bootbank/         dev/              lib64/            mbr/              productLocker/    store/            tmp/              vmfs/
altbootbank/      bootpart.gz       etc/              local.tgz         opt/              sbin/             tardisks.noauto/  usr/              vmimages/
[juan@fractal:~] esxcli software sources profile list -d /vmfs/volumes/fractal_hdd/VMware-ESXi-7.0U3g-20328353-depot.zip
Name                           Vendor        Acceptance Level  Creation Time        Modification Time

---

ESXi-7.0U3g-20328353-standard  VMware, Inc.  PartnerSupported  2022-09-01T00:00:00  2022-09-01T00:00:00
ESXi-7.0U3g-20328353-no-tools  VMware, Inc.  PartnerSupported  2022-09-01T00:00:00  2022-08-23T03:00:16
[juan@fractal:~]
```

## Install Update

``` shell
[juan@fractal:~] esxcli software profile update -d /vmfs/volumes/fractal_hdd/VMware-ESXi-7.0U3g-20328353-depot.zip -p ESXi-7.0U3g-20328353-standard
```

