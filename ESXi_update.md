ESXi/vSphere CLI Update

```shell
[root@HDC-ESX01:~] vmware -v
VMware ESXi 7.0.3 build-19482537
```

```shell
# installation (update) date of the ESXi image
[root@HDC-ESX01:~] esxcli software vib list | grep 'Install\|esx-base'

Name                           Version                               Vendor   Acceptance Level  Install Date
esx-base                       7.0.3-0.35.19482537                   VMware   VMwareCertified   2022-06-08-08
```

```shell
# check the version of the profile you are using
[root@HDC-ESX01:~] esxcli software profile get
HPE-Custom-AddOn_703.0.0.10.9.0-11            # this is what we are looking for
   Name: HPE-Custom-AddOn_703.0.0.10.9.0-11
   Vendor: Hewlett Packard Enterprise
   Creation Time: 2022-06-08T14:43:53
   Modification Time: 2024-08-13T11:03:55
   Stateless Ready: True: True
```

```shell
# Enable the rule in the firewall to allow the ESXi host to access the Internet:
esxcli network firewall ruleset set -e true -r httpClient
```

List available images in the VMware online depot

The images with `s` in the name contain security patches only (ESXi-8.02`s`b-xxxxx). We will upgrade to **ESXi 8.0U2b-23305546-standard**, which includes all patches and new VMware Tools.

```
 [root@HDC-ESX01:~] esxcli software sources profile list --depot=https://hostupdate.vmware.com/software/VUM/PRODUCTION/main/vmw-depot-index.xml
Name                              Vendor        Acceptance Level  Creation Time        Modification Time
--------------------------------  ------------  ----------------  -------------------  -----------------
ESXi-6.7.0-20201004001-no-tools   VMware, Inc.  PartnerSupported  2022-09-21T13:35:37  2022-09-21T13:35:37 
...
ESXi-7.0U3c-19193900-standard     VMware, Inc.  PartnerSupported  2022-01-18T00:00:00  2022-01-18T00:00:00
ESXi-7.0U3c-19193900-no-tools     VMware, Inc.  PartnerSupported  2022-01-18T00:00:00  2022-01-12T00:03:42
ESXi-7.0U3sd-19482531-standard    VMware, Inc.  PartnerSupported  2022-03-29T00:00:00  2022-03-29T00:00:00
ESXi-7.0U3sd-19482531-no-tools    VMware, Inc.  PartnerSupported  2022-03-29T00:00:00  2022-03-11T13:53:29
ESXi-7.0U3d-19482537-standard     VMware, Inc.  PartnerSupported  2022-03-29T00:00:00  2022-03-29T00:00:00 # current installed version
ESXi-7.0U3d-19482537-no-tools     VMware, Inc.  PartnerSupported  2022-03-29T00:00:00  2022-03-11T15:01:02 # current installed version
ESXi-7.0U3e-19898904-standard     VMware, Inc.  PartnerSupported  2022-06-14T00:00:00  2022-06-14T00:00:00
ESXi-7.0U3e-19898904-no-tools     VMware, Inc.  PartnerSupported  2022-06-14T00:00:00  2022-06-03T07:44:29
ESXi-7.0U3sf-20036586-standard    VMware, Inc.  PartnerSupported  2022-07-12T00:00:00  2022-07-12T00:00:00
ESXi-7.0U3sf-20036586-no-tools    VMware, Inc.  PartnerSupported  2022-07-12T00:00:00  2022-06-30T13:58:41
ESXi-7.0U3f-20036589-standard     VMware, Inc.  PartnerSupported  2022-07-12T00:00:00  2022-07-12T00:00:00
ESXi-7.0U3f-20036589-no-tools     VMware, Inc.  PartnerSupported  2022-07-12T00:00:00  2022-06-30T15:28:40
ESXi-7.0U3g-20328353-standard     VMware, Inc.  PartnerSupported  2022-09-01T00:00:00  2022-09-01T00:00:00
ESXi-7.0U3g-20328353-no-tools     VMware, Inc.  PartnerSupported  2022-09-01T00:00:00  2022-08-23T03:00:16
ESXi-7.0U3si-20841705-standard    VMware, Inc.  PartnerSupported  2022-12-08T00:00:00  2022-12-08T00:00:00
ESXi-7.0U3si-20841705-no-tools    VMware, Inc.  PartnerSupported  2022-12-08T00:00:00  2022-11-24T01:25:32
ESXi-7.0U3i-20842708-standard     VMware, Inc.  PartnerSupported  2022-12-08T00:00:00  2022-12-08T00:00:00
ESXi-7.0U3i-20842708-no-tools     VMware, Inc.  PartnerSupported  2022-12-08T00:00:00  2022-11-24T05:06:48
ESXi-7.0U3j-21053776-standard     VMware, Inc.  PartnerSupported  2023-01-31T00:00:00  2023-01-31T00:00:00
ESXi-7.0U3j-21053776-no-tools     VMware, Inc.  PartnerSupported  2023-01-31T00:00:00  2023-01-02T12:08:15
ESXi-7.0U3k-21313628-standard     VMware, Inc.  PartnerSupported  2023-02-21T00:00:00  2023-02-21T00:00:00
ESXi-7.0U3k-21313628-no-tools     VMware, Inc.  PartnerSupported  2023-02-21T00:00:00  2023-02-18T08:57:33
ESXi-7.0U3sl-21422485-standard    VMware, Inc.  PartnerSupported  2023-03-30T00:00:00  2023-03-30T00:00:00
ESXi-7.0U3sl-21422485-no-tools    VMware, Inc.  PartnerSupported  2023-03-30T00:00:00  2023-03-10T16:04:06
ESXi-7.0U3l-21424296-standard     VMware, Inc.  PartnerSupported  2023-03-30T00:00:00  2023-03-30T00:00:00
ESXi-7.0U3l-21424296-no-tools     VMware, Inc.  PartnerSupported  2023-03-30T00:00:00  2023-03-11T01:18:32
ESXi-7.0U3m-21686933-standard     VMware, Inc.  PartnerSupported  2023-05-03T00:00:00  2023-05-03T00:00:00
ESXi-7.0U3m-21686933-no-tools     VMware, Inc.  PartnerSupported  2023-05-03T00:00:00  2023-04-28T16:03:19
ESXi-7.0U3n-21930508-standard     VMware, Inc.  PartnerSupported  2023-07-06T00:00:00  2023-07-06T00:00:00
ESXi-7.0U3n-21930508-no-tools     VMware, Inc.  PartnerSupported  2023-07-06T00:00:00  2023-06-15T12:39:40
ESXi-7.0U3so-22348808-standard    VMware, Inc.  PartnerSupported  2023-09-21T00:00:00  2023-09-21T00:00:00
ESXi-7.0U3so-22348808-no-tools    VMware, Inc.  PartnerSupported  2023-09-21T00:00:00  2023-08-30T10:30:54
ESXi-7.0U3o-22348816-standard     VMware, Inc.  PartnerSupported  2023-09-21T00:00:00  2023-09-21T00:00:00
ESXi-7.0U3o-22348816-no-tools     VMware, Inc.  PartnerSupported  2023-09-21T00:00:00  2023-08-30T12:02:18
ESXi-7.0U3p-23307199-standard     VMware, Inc.  PartnerSupported  2024-03-05T00:00:00  2024-03-05T00:00:00
ESXi-7.0U3p-23307199-no-tools     VMware, Inc.  PartnerSupported  2024-03-05T00:00:00  2024-02-14T13:03:18
ESXi-7.0U3sq-23794019-standard    VMware, Inc.  PartnerSupported  2024-05-21T00:00:00  2024-05-21T00:00:00
ESXi-7.0U3sq-23794019-no-tools    VMware, Inc.  PartnerSupported  2024-05-21T00:00:00  2024-05-03T09:46:32
ESXi-7.0U3q-23794027-standard     VMware, Inc.  PartnerSupported  2024-05-21T00:00:00  2024-05-21T00:00:00
ESXi-7.0U3q-23794027-no-tools     VMware, Inc.  PartnerSupported  2024-05-21T00:00:00  2024-05-03T11:03:38 
...
ESXi-8.0.0-20513097-standard      VMware, Inc.  PartnerSupported  2022-09-23T18:59:28  2022-09-23T18:59:28
```

Put the host into maintenance mode:

`esxcli system maintenanceMode set --enable=true`

Upgrade the ESXi image to the specified build online:

`esxcli software profile update -p ESXi-8.0U2b-23305546-standard -d https://hostupdate.vmware.com/software/VUM/PRODUCTION/main/vmw-depot-index.xml -no-hardware-warning`

Disable the Internet access:

`esxcli network firewall ruleset set -e false -r httpClient`

Reboot the ESXi server:

`reboot`

Disable the maintenance mode:

`esxcli system maintenanceMode set --enable=false`

Check that the ESXi build has been updated:

`esxcli software profile get`
