# X-MAS CTF 2020 - Forensics Challenge - DESTRUCTION

>-Challenge-</br>
>DESTRUCTION</br>
>DESTROYED! Somebody has stolen gnome's files :( omegasad please help...</br>

To kick things off we download a file called "destroyed.elf"

I found it best to convert it from an elf file to a raw file to analyze with volatility

first we need to run objdump and find the first LOAD section where main memory reference is
```
objdump -h destroyed.elf | egrep -w "(Idx|load1)"
```
Next we extract the RAM, getting rid of the first bytes
```
size=0x80000000;off=0x25d0;head -c $(($size+$off)) destroyed.elf|tail -c +$(($off+1)) > dest.raw
```
![objdump](https://i.ibb.co/7RJyjQj/2020-12-18-19h49-47.png)

Now we can start analyzing with volatility and really dig into the system!
First things first we need to find the profile by running
```
python vol.py -f dest.raw imageinfo
```
![Volatility Imageinfo](https://i.ibb.co/314Vdx4/2020-12-18-20h00-14.png)

Now that we have a profile we can use we can start analyzing the filesystem.
Whenever I check the filesystem for the first time, I like to grep the "Desktop" that way I'm not overbeared with system files and we can focus a little more on indpendent areas of the system
```
python vol.py -f dest.raw --profile=Win7SP1x64 filescan | grep "Desktop"
```
And right away we find a file named "test.txt" on the desktop of User "Johnbi"
![Volatility Filescan](https://i.ibb.co/K6xKcKK/2020-12-18-20h10-10.png)

Now lets try and extract the file and see what we find
```
python vol.py -f dest.raw --profile=Win7SP1x64 dumpfiles -D . -Q 0x7e9e91b0
```
And now we managed to extract the file named "file.None.0xblahblah"
![Volatility Dumpfiles](https://i.ibb.co/gWyQQZ8/2020-12-18-20h14-58.png)

Now if we just read that file we see this string
```
582D4D41537B7468616E6B73666F7264756D70696E6762726F7D
```

Lets throw that over to [Cyberchef](https://gchq.github.io/CyberChef/)
And look at that! CyberChef finds that it is a Hex and decodes it into the flag!
```
X-MAS{thanksfordumpingbro}
```
![Cyberchef](https://i.ibb.co/L011MZf/2020-12-18-20h20-03.png)

