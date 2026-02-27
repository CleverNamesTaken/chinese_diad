# Chinese Diad - Proxy DLL for injecting XOR encrypted shellcode

## TLDR

Bypass antivirus by separating your shellcode from your loader, and executing it through a DLL hijack.

There are many other DLL proxying tools out there, but I had to modify each of them a bit to get what I wanted so I decided to just consolidate them into my own.

## Introduction

This tool was largely inspired by the [PlugX triad](https://www.sentinelone.com/labs/moshen-dragons-triad-and-error-approach-abusing-security-software-to-sideload-plugx-and-shadowpad/) where Chinese APTs used DLL search order hijacking to decrypt a local payload.

DLL hijacking is an older technique and likely won't go away until DLL signing is enforced.

## Usage

1. Identify the DLL that you want to proxy and pull it down.
1. Generate the shellcode that you want to execute.
1. Use chinese_diad to create your proxy dll and encrypted shellcode blob.
1. Push your proxy dll and shellcode blob to your target and trigger the hijack.
1. Profit.

```
usage: chinese_diad.py [-h] -i INPUT -o OUTPUT -x ORIGINAL [-k XOR_KEY]
                       -p SHELLCODE_FILE [-n] [-d]

Build a proxy DLL that executes XOR encrypted shellcode.

options:
  -h, --help            show this help message and exit
  -i, --input INPUT     Input DLL file
  -o, --output OUTPUT   Output directory
  -x, --original ORIGINAL
                        Path to original DLL on target
  -k, --xor-key XOR_KEY
                        XOR key used to encrypt the shellcode
  -p, --shellcode-file SHELLCODE_FILE
                        Path to the unencrypted shellcode. This will also be
                        the name of the encrypted shellcode that will be
                        loaded by the dll.
  -n, --no-clean        Delete the source files after compiling
  -d, --debug           Compile in debug mode. This implies no clean up.


python3 chinese_diad.py -i dnsapi.dll -o /tmp/chinese_diad -x 'C:\Windows\System32\dnsapi.dll' -k 'PLUGX' -p /tmp/shellcode.bin
```

## References

- github.com/Print3M/DllShimmer
- github.com/Pascal-0x90/sideloadr
- github.com/tothi/dll-hijack-by-proxying
- https://hijacklibs.net/
