# NetworkAnalysis-WebShell
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
We then go to TCP tab. Already we see that addr .4 is hitting Addr .5 with source port 41675
<img width="769" height="495" alt="Screenshot 2026-08-25 160211" src="https://github.com/user-attachments/assets/89be5920-e4a2-4fb1-8021-c4b6620e4f61" />
