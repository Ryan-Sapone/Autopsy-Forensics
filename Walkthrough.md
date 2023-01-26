<h1>Disk Analysis & Autopsy</h1>
A walkthrough of how I solved this room on TryHackMe, the last room in the Cyber Defense learning path. This is how to utilize Autopsy to investigate artifacts from a disk image. 
<hr>
<h2>Setup</h2>
We're going to open Autopsy and load the case file provided by TryHackMe. 
<img src="https://user-images.githubusercontent.com/107446796/214723702-262d7849-b540-4b5a-8c20-6e0011770296.png">

Now we load the missing image, as instructed to do in the room.
<img src="https://user-images.githubusercontent.com/107446796/214723888-7db6a2a0-a0b8-4842-a56c-6442dbac3b59.png">

<h2>Q1: What is the MD5 hash of the E01 image?</h2>
We can see the data source of the .E01 image, and when we look at the metadata we get the MD5 hash of 3f08c518adb3b5c1359849657a9b2079. 
<img src="https://user-images.githubusercontent.com/107446796/214724551-533b8b8e-62d0-4a37-bab8-85acf8ab25c3.png">

<h2>Q2: What is the computer account name?</h2>
We can check the operating system information to find the hostname. I believe this question is worded poorly, but TryHackMe was asking for the computer's name, not a particular account name. DESKTOP-0R59DJ3.
<img src="https://user-images.githubusercontent.com/107446796/214725481-4b4d2926-18b9-4994-83d2-d36d0ceac19b.png">

<h2>Q3: List all the user accounts. (alphabetical order)</h2>
If we look at the operating system user accounts section, we can see the SAM source file list all of the user accounts on the disk. TryHackMe is not looking for the Administrator or Guest accounts, and so we will only be looking at the usernames that have been highlighted. The answer in order: H4S4N,joshwa,keshav,sandhya,shreya,sivapriya,srini,suba. 
<img src="https://user-images.githubusercontent.com/107446796/214726393-d809eded-9b93-4ad5-ac69-88981cc9a93a.png">

<h2>Q4: Who was the last user to log into the computer?</h2>
Still in this section, we can sort the column by date accessed and see sivapriya was the most recent one to access the system. 
<img src="https://user-images.githubusercontent.com/107446796/214726715-00f9e567-3adb-4c85-a2bb-2be9a1f776aa.png">

<h2>Q5: What was the IP address of the computer?</h2>
This question gave me a bit of a challenge. At first, I tried looking at the system information, but that turned out to be pointless. I then thought about maybe searching for registry values, but there was too much info for me to sift through, so I figured I was on the wrong path. I started doing some Google searches on utilizing Autopsy to view the host IP address, and I stumbled upon a document on exploit-db (https://www.exploit-db.com/docs/48254). In this document, one of the questions was to find the hostname, MAC address, and IP address. They talked about the irunin.ini file, which I also saw mentioned elsewhere during my Google searches. From here, I ended up searching for irunin.ini in Autopsy, and I found out that .INI files are configuration files. In this case, it lists the LANIP of 192.168.130.216 which is the information we need. More info about .INI files (https://en.wikipedia.org/wiki/INI_file). 
<img src="https://user-images.githubusercontent.com/107446796/214729651-cae8ba06-9700-43ed-a4ee-150874417a92.png">

<h2>Q6: What was the MAC address of the computer? (XX-XX-XX-XX-XX-XX)</h2>
This information is also found in the same file as the above section labeled as LANNIC. 08-00-27-2C-C4-B9
<img src="https://user-images.githubusercontent.com/107446796/214730056-cf27b86b-f9b5-4013-842a-53cee8ef1b8a.png">

<h2>Q7: What is the name of the network card on this computer?</h2>
Again, I was having a little trouble figuring out where to find this information. However, my thought was the registry keys. I checked online to see where exactly I can access the registry keys for the NICs, and this article was able to tell me where I needed to navigate to find the information - HTTP warning (http://what-when-how.com/windows-forensic-analysis/registry-analysis-windows-forensic-analysis-part-3/). This will be in the following path: ROOT > Microsoft > Windows NT > CurrentVersion > NetworkCards. Intel(R) PRO/1000 MT Desktop Adapter.
<img src="https://user-images.githubusercontent.com/107446796/214731593-55ee38e9-0a8e-41b9-9b6d-cbdc7f70b3b3.png">

<h2>Q8: What is the name of the network monitoring tool?</h2>
I thought this would be Wireshark, but I guess not. By looking at the installed programs on the machine, we can go through the 41 results and find something called Look@LAN. Here's what Google says:
<img src="https://user-images.githubusercontent.com/107446796/214732173-850af8c0-c9ef-45be-b543-7287057e3858.png">
<img src="https://user-images.githubusercontent.com/107446796/214732216-ef7303fc-d67c-4797-9e03-1c89e6afa622.png">

<h2>Q9: A user bookmarked a Google Maps location. What are the coordinates of the location?</h2>
An easy one that I didn't need to research. Autopsy shows web bookmarks. Head into that section and you can see the Google Maps bookmark. 12°52'23.0"N 80°13'25.0"E.
<img src="https://user-images.githubusercontent.com/107446796/214732655-46fcf70c-541a-4e76-923c-399d3e3f50d7.png">

<h2>Q10: A user has his full name printed on his desktop wallpaper. What is the user's full name?</h2>
This took me about 20 minutes to look at all the pictures. By using the Images/Video tab in Autopsy, you can check each user's Downloads. After looking through a few pictures, you'll stumble upon Anto Joshwa and their cyberpunk picture with the name in the top left corner.
<img src="https://user-images.githubusercontent.com/107446796/214733768-e0537e8f-d763-4094-9486-33264aac8573.png">

<h2>Q11: A user had a file on her desktop. It had a flag but she changed the flag using PowerShell. What was the first flag?</h2>
A little more research into where to find PowerShell command history revealed that it's in the AppData > Roaming > Microsoft > PowerShell directory. I thought it was H4S4N at first, since he seems to have a thing for hackers (like Mr. Robot in his Downloads folder), but turns out it was Shreya who has the PowerShell history of changing flags. The original flag was flag{HarleyQuinnForQueen}. 
<img src="https://user-images.githubusercontent.com/107446796/214734584-d0396937-225b-4156-8f4b-b6d8483d9bb5.png">

<h2>Q12: The same user found an exploit to escalate privileges on the computer. What was the message to the device owner?</h2>
We know from the last question that Shreya was the user from the last question. By looking at each directory under the user, we finally come across a PowerShell file on the Desktop. Flag{I-hacked-you}.
<img src="https://user-images.githubusercontent.com/107446796/214735010-8728938a-cdcf-44f6-9eb3-f00bca036a11.png">

<h2>Q13: 2 hack tools focused on passwords were found in the system. What are the names of these tools? (alphabetical order)</h2>
Full disclosure, I was stuck on where to look here. I ended up getting some help on this and was told to check Windows Defender logs. These can be found in Program Data > Microsoft > Windows Defender > Scans > History > Service > DetectionHistory. I went through the first two folders and didn't find anything, but in folder 02, I saw the two tools: Mimkatz and LaZagne.
<img src="https://user-images.githubusercontent.com/107446796/214736243-d71ba371-e5d7-4785-bc1b-30307da38b12.png">
<img src="https://user-images.githubusercontent.com/107446796/214736286-c5bb602d-b552-49e9-8a2a-a1b350b06cb6.png">

<h2>Q14: There is a YARA file on the computer. Inspect the file. What is the name of the author?</h2>
I searched for .YAR, since YARA files are saved with this extension (thanks YARA room on TryHackMe!). I found a file with the .LNK extension belonging to H4S4N that matched with the .YAR extension as well. I did another keyword search for the full file name and found the results. 
<img src="https://user-images.githubusercontent.com/107446796/214737899-e79f656d-b769-4d7c-b82a-05662a8804e1.png">
<img src="https://user-images.githubusercontent.com/107446796/214737973-03369c5c-d7ec-4611-b314-5917391001fd.png">

<h2>Q15: One of the users wanted to exploit a domain controller with an MS-NRPC based exploit. What is the filename of the archive that you found? (include the spaces in your answer)</h2>
So TryHackMe said this was an MS-NRPC based exploit, so I Googled MS-NRPC exploits, and the first thing that popped up is ZeroLogon. I searched for this and ended up finding an artifact in the recent documents of the user Sandhya. The full file name is 2.2.0 20200918 Zerologon encrypted.zip for the answer, and for the completion of this challenge.
<img src="https://user-images.githubusercontent.com/107446796/214739305-88125a13-581e-44eb-8744-6d13e103b0a7.png">

<hr>

<h2>Overview</h2>
This was a really fun room! This was the last room I had to complete in order to earn my certificate of completion for the Cyber Defense pathway. I have utilized Autopsy in the National Cyber League challenges as well as with labs for school. This was a great room and taught me a lot about where I can find certain information in a file system and while performing a forensic investigation. Although I had to call upon help and do some research for some of the questions, I still feel like I had a solid understanding of how to navigate through Autopsy. 


