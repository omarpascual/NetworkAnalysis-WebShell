<img width="1181" height="542" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/11e2704d-21cf-4473-a7ae-68108c562813" /># NetworkAnalysis-WebShell
This is a retired lab located with BlueTeamLabs
We first download the file from their website and unzip it in a controlled environment.
We use wireshark to analyze the pcap. The First thing i like to do is look at the Capture File Properties by going to STATISTICS at the top.
<img width="1223" height="933" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/c39e87db-6064-4c4c-b3b8-a6a089040eb9" />

We see that the first packet was captured from 2021-02-07 11:31:22
Last packet captured was 2021-02-07 11:46:31
Its important to see the time frame you are investigating incase you are asked to investigate a pcap with a certain time frame and you want to double check you are investigating the right file.
<img width="789" height="825" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/1ebc6e3d-7f1f-406c-8ffd-780f12f929e9" />
The next we want to check is the Protocol Hierarchy, which is also in the STATISTICS dropdown.
We see that there was SMB(Server Message Block Protocol), DomainNameSystem, SSH Protocol, and HTTP
<img width="941" height="686" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/a9cc5dca-f725-49ce-a160-126680a581cd" />
Next we want to see the Conversations, also located in the STATISTICS dropdown. We want to see what conversations were established. We go the IPv4 tab, and sort it by byte. We see that 10.251.96.4 conversed with 10.251.96.5 and 172.20.10.5 with 172.20.10.2
<img width="1194" height="492" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/51a9c793-ae82-45e2-97f3-88b50b1cadba" />
We then go to TCP tab. Already we see that addr .4 is hitting Addr .5 with source port 41675. We also see that port 80 the bytes are 184 while the others are 118. Same with port 22 if you go down 8. With this we can confirm that 10.251.96.5 has port 80 and 22 open
<img width="955" height="472" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/42e65226-1534-4f7a-9f79-37a4821fb3be" />
If we scroll down to the bottom, we see that addr .5 is calling back to addr .4 on port 4422. Below it, we see external IPs 34 and 35. We make a note of those IPs incase.
<img width="1108" height="487" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/dc40d332-ecbf-410c-8614-006ef8de72c2" />
Now lets investigate the Pcap. We first see on packet 14 that it is HTTP, so we right click go to follow and we will follow TCP stream
<img width="1187" height="623" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/33f3b257-a00d-4205-9bee-061eb473789d" />
This will show communication between client and server. We see that 172.20.10.2 is a Ubuntu server.
<img width="828" height="437" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/32dd43c0-88eb-4050-ab60-621f5fb6de0a" />
Scrolling back down on packet #38 we see a POST request. We look into its HTTP stream and see Username:admin and password:Admin%401234. we decode %40 with URL decoder and get @ so we assume the password is Admin@1234
<img width="1200" height="406" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/e254cb88-3533-4ca2-bece-ac847e092953" />
Scrolling down we see that in Packet 117 at 2021-02-07 at 16:33:06 with 10.251.96.4 --> 10.251.96.5
<img width="1170" height="302" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/f760a409-f865-4584-a8af-4a0944a884f8" />
On Packet 151 we see another SYN/ACK on port 22
<img width="1174" height="387" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/10248d94-e701-451c-84d7-4e0df2fe4e61" />
The last packet scan was on packet 2166 at 2021-02-07 at 16:33:06
Going down to packet 2172 we see the first communication from addr .4 to .5 on port 80.
<img width="1182" height="259" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/b76e825b-dc45-46a2-aeec-8c5db3257602" />
We scroll down to see the POST request on packet 2190. We follow the HTTP STREAM and see a new username and password with %27. We decode it and it is a single quote '
<img width="347" height="105" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/3649497e-6233-4516-828d-f0115d2eb770" />
</br>
</br>
On packet 2215 we see a GET Request and if we follow the HTTP STREAM, we see that the User-Agent is no long Mozillabut gobuster/3.0.1. We make a note of the time 16:34:05
<img width="1166" height="277" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/b6ce6982-c9ef-475d-b610-cf7a942d7545" />
After looking into this we want to look at response code of 200 on IP addr 10.251.96.5 and so we filter it so it only shows those. What we see in the Lenth column, that the numbers are between 623-725. But then on Packet 7725 and 13894 we see an anomoly. Their length is 8494 and 8977 which means that the server responded with something quite large.
<img width="1179" height="696" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/88f40352-69eb-4605-866e-5ff00584980b" />
We also want to see when the last time goBuster did a GET request. We see on packet 13661 at 16:34:06 was the last time.
Scrolling down on Packet 13737 we start to see a couple of browse Chocolate
<img width="1189" height="629" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/a9431640-1058-43ed-9943-574cf3f654e5" />
Packet 13886 we start to see the 10.251.96.4 again and on packet 13889 we see them viewing a info.php, and going to packet 13914 we see there is an uploads.
<img width="1181" height="542" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/edba5140-7fd2-47ee-baa6-5d20523a962d" />
Since there is a uploads directory we also want to see if there is POST directory. Scrolling down, we do see POST on packet 13979 at 16:36:17
<img width="1179" height="368" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/c75e4119-5445-4ecb-95c4-99ed8cbde254" />
We follow the HTTP STREAM and we see that the user agent has changed to sqlmap/1.4.7 (Sqlmap is a tool that automates sql attacks) They also tried to log in using user and pass
<img width="1196" height="523" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/13e3b408-f805-4e0f-857e-1a9c5251e7ea" />
We find another POST request on packet 14060 with a weird post request. We follow it and we see that it can be some kind of sql attack happening at 16:36:51
<img width="1196" height="519" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/6245e2c7-a6c4-4f7a-acdd-10457cb02a90" />
We see that it is encoded so we decode it and get this.
We see that at the end we see their objective is to open a shell to read the file /etc/passwd
<img width="969" height="738" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/ce019e60-91d7-417b-a4ec-faec11a8493b" />
The last POST request from sqlmap happened on packet 15978 at 16:37:28. We want to see if addr 10.251.96.4 uploaded anything onto the web server because they did visit an upload directory. We enter http.request.method == POST && ip.addr == 10.251.96.4 and on packet 16102 at 16:40:39 we see an upload.php (application/x-php)
We follow the stream and see the Referer with editprofile.php so we can assume the pressed editprofile
 and theres a button called upload. They clicked on the upload button and uploaded the file dbfunctions.php. It was uploaded successfully.
 <img width="1210" height="591" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/c522d269-8c72-4ec7-a3a7-b839367d2b09" />
On packet 16121 we get an GET /uploads/dbdunctions.php and Packet 16134 we see a GET /uploads/dbfunctions.php?cmd=id
<img width="1169" height="423" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/97804b80-22b1-42c9-890c-f084b518cd49" />
We follow packet 16134 and see the uid.
<img width="826" height="455" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/3467d1e4-3c0c-42f7-93d1-f2c69ad60672" />


