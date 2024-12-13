<body>
    <h1>Investigating Windows</h1>
    <p>This project explores a forensic investigation of a compromised Windows system. Using tools like PowerShell, we uncover important details such as the Windows version, logged-in users, and more.</p>
    <div class="highlight">
        <strong>Lab Link:</strong> <a href="https://tryhackme.com/r/room/investigatingwindows" target="_blank">TryHackMe: Investigating Windows</a><br>
        <strong>Tool:</strong> PowerShell
    </div>
    <h2>1. What’s the version and year of the Windows machine?</h2>
    <p>To determine the Windows version and year, use the <code>Get-ComputerInfo</code> command.</p>
    <pre><code>Get-ComputerInfo</code></pre>
    <p><strong>Result:</strong></p>
    <img width="885" alt="Screenshot 2024-11-22 at 1 53 14 PM" src="https://github.com/user-attachments/assets/556130ef-8621-4522-9485-bb4d5d524412">
    <p>To narrow the output to only properties starting with "OS," use:</p>
    <pre><code>Get-ComputerInfo -Property "OS*"</code></pre>
    <p><strong>Result:</strong></p>
    <img width="965" alt="Screenshot 2024-11-22 at 1 57 13 PM" src="https://github.com/user-attachments/assets/4ce3f730-6ddd-49e6-8f22-b3a173c672a5">
    <p><strong>Answer:</strong> Windows Server 2016</p>
    <h2>2. Which user logged in last?</h2>
    <p>To identify the last logged-in user, you can enumerate all local users or examine login events.</p>
    <h3>Option 1: Enumerate Local Users</h3>
    <pre><code>Get-LocalUser</code></pre>
    <p><strong>Result:</strong></p>
    <img width="653" alt="Screenshot 2024-11-22 at 2 04 18 PM" src="https://github.com/user-attachments/assets/4815d6ee-1f88-4f66-93cc-2405839b95e2">
    <h3>Option 2: Check Event Viewer</h3>
    <ol>
        <li>Open Event Viewer with the PowerShell command:</li>
        <pre><code>eventvwr</code></pre>
        <li>Navigate to <strong>Windows Logs > Security</strong> and filter by Event ID <strong>4624</strong> (successful logons).</li>
    </ol>
    <p><strong>Result:</strong></p>
    <img width="690" alt="Screenshot 2024-11-22 at 2 16 35 PM" src="https://github.com/user-attachments/assets/59f6ee6c-25be-4617-90c6-d0f764df4b85">
    <p><strong>Answer:</strong> Administrator</p>
    <h2>3. When did John log onto the system last?</h2>
    <p>To find the last logon time for John, use the following command:</p>
    <pre><code>net user John | findstr "last"</code></pre>
    <p><strong>Result:</strong></p>
    <img width="859" alt="Screenshot 2024-11-22 at 3 35 47 PM" src="https://github.com/user-attachments/assets/2bfb8186-5b26-4b86-8ea8-a2874c06bd2b">
    <p><strong>Answer:</strong> 03/02/2019 05:48:32 PM</p>
    <h2>4. When did Jenny last logon?</h2>
    <p>To find Jenny's last logon time, use the following command:</p>
    <pre><code>net user "Jenny" | findstr "Last"</code></pre>
    <p><strong>Result:</strong></p>
     <img width="527" alt="Screenshot 2024-11-22 at 4 10 54 PM" src="https://github.com/user-attachments/assets/2417dda8-6e32-4ad5-9ba9-b2c0bbf129db">
    <p><strong>Answer:</strong> (Add exact date and time after capturing the screenshot)</p>
    <h2>5. What two accounts had administrative privileges (other than the Administrator user)?</h2>
    <p>To find accounts with administrative privileges, use the following command:</p>
    <pre><code>Get-LocalGroupMember -Group "Administrators"</code></pre>
    <p><strong>Result:</strong></p>
    <img width="777" alt="Screenshot 2024-11-22 at 4 05 32 PM" src="https://github.com/user-attachments/assets/13fbf1b5-7b0b-4043-8709-9a90de7e9fc6">
    <p><strong>Answer:</strong> Jenny, Guest</p>
    <h2>6. What IP does the system connect to when it first starts?</h2>
    <p>To find this, follow these steps:</p>
    <ol>
        <li>Open Registry Editor by typing the command:</li>
        <pre><code>regedit</code></pre>
        <li>Navigate to the path:</li>
        <pre><code>HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\WINDOWS\CURRENT VERSION\RUN</code></pre>
    </ol>
    <p>The Run registry key specifies programs that are launched automatically when a user logs in. These often include essential utilities or third-party applications.</p>
    <p><strong>Result:</strong></p>
   <img width="1105" alt="Screenshot 2024-11-22 at 3 50 27 PM" src="https://github.com/user-attachments/assets/391377b2-3f67-4377-abfd-70c34922ee29">
    <p><strong>Answer:</strong> 10.34.2.3</p>
    <h2>7. What’s the name of the scheduled task that is malicious?</h2>
    <p>Attackers often use the root folder (<code>\</code>) in Task Scheduler Library to hide malicious tasks. To view tasks stored in the root folder, use the following command:</p>
    <pre><code>Get-ScheduledTask | where {$_.TaskPath -eq “\”}</code></pre>
    <p><strong>Result:</strong></p>
    <img width="752" alt="Screenshot 2024-11-22 at 4 49 55 PM 1" src="https://github.com/user-attachments/assets/443a14d6-13ba-4337-b18a-c99e0662ff83">
    <h3>Common Characteristics of Malicious Tasks</h3>
    <ul>
        <li>Unusual or gibberish names</li>
        <li>Reference to unknown executables or scripts</li>
        <li>Trigger actions at suspicious times (e.g., every minute or at user logon)</li>
    </ul>
    <h3>Investigating the Task's Referenced File</h3>
    <p>If the task references an executable or script, hash the file and scan it on VirusTotal or a similar tool:</p>
    <ol>
        <img width="833" alt="Screenshot 2024-11-22 at 4 59 58 PM" src="https://github.com/user-attachments/assets/d059c550-110c-42ff-8abb-92296c390386">
        <pre><code>Get-FileHash -Algorithm SHA256 -Path "C:\Path\To\File"</code></pre>
        <p>Upload the hash to <a href="https://www.virustotal.com" target="_blank">VirusTotal</a> to analyze the file.</p>
        <img width="1828" alt="Screenshot 2024-11-22 at 5 12 50 PM" src="https://github.com/user-attachments/assets/43f4ff1b-e22e-4919-a7d7-ba7061a81c42">
    </ol>
    <p><strong>Answer:</strong> Clean file system</p>
    <h2>8. What file was the task trying to run daily?</h2>
    <p>To retrieve a specific scheduled task from the Task Scheduler Library and identify the program it is running, use the following PowerShell commands:</p>
    <ol>
        <li>Assign the task to a variable:</li>
        <pre><code>$task = Get-ScheduledTask | Where-Object TaskName -EQ "Clean file system"</code></pre>
        <li>View the action associated with the task:</li>
        <pre><code>$task.Actions</code></pre>
    </ol>
    <p><strong>Result:</strong></p>
    <img width="841" alt="Screenshot 2024-12-01 at 10 20 26 PM" src="https://github.com/user-attachments/assets/dd4a824c-96c9-421f-bc50-f4ceb5a7e71f">
    <p><strong>Answer:</strong> nc.ps1</p>
    <h2>9. What port did this file listen locally for?</h2>
    <p>The port number is found in the <strong>Arguments</strong> column from the previous command output:</p>
    <img width="763" alt="Screenshot 2024-12-01 at 10 27 53 PM" src="https://github.com/user-attachments/assets/581e8e77-a796-41a9-ae69-ac950d5a3235">
    <p><strong>Answer:</strong> 1348</p>
    <h2>10. When did Jenny last logon?</h2>
    <p>To find Jenny's last logon time, use the following command:</p>
    <pre><code>net user Jenny | findstr "Last"</code></pre>
    <p><strong>Result:</strong></p>
    <img width="603" alt="Screenshot 2024-12-01 at 10 57 38 PM" src="https://github.com/user-attachments/assets/c6fdb7a5-c1ec-4ac7-be2e-486d9f058dea">
    <p><strong>Answer:</strong> Never</p>
    <h2>11. At what date did the compromise take place?</h2>
    <h3>Method 1: Using `net user` command</h3>
    <p>To find details about the `Last Set` field or other time-related entries for Jenny, use:</p>
    <pre><code>net user jenny</code></pre>
    <p><strong>Result:</strong></p>
    <img width="619" alt="Screenshot 2024-12-01 at 11 04 44 PM" src="https://github.com/user-attachments/assets/a1ddfe79-25a2-4512-944a-a519b33d1d5f">
    <h3>Method 2: Analyzing File Explorer Metadata</h3>
    <p>Open File Explorer and navigate to <code>Local Disk (C:)</code>. Look for the recurring date in the folder properties.</p>
    <p><strong>Result:</strong></p>
    <img width="1090" alt="Screenshot 2024-12-01 at 11 17 20 PM" src="https://github.com/user-attachments/assets/34bc8e43-0ee5-41c7-afce-0c0eb274e900">
    <p><strong>Answer:</strong> 03/02/2019</p>
    <h2>12. During the compromise, at what time did Windows first assign special privileges to a new logon?</h2>
    <p>To determine the time when special privileges were assigned to a new logon, follow these steps:</p>
    <ol>
        <li>Open Event Viewer by typing the command:</li>
        <pre><code>eventvwr</code></pre>
        <li>Click <strong>Create Custom View</strong> on the right side of the Event Viewer window.</li>
        <li>Set the <strong>Custom Range</strong> to:
            <ul>
                <li>Date: 03/02/2019</li>
                <li>Time: 4:00:00 PM – 5:00:00 PM</li>
            </ul>
        </li>
        <img width="1535" alt="Screenshot 2024-12-01 at 11 35 10 PM" src="https://github.com/user-attachments/assets/145e1127-ce33-47a4-881b-715d7105c7c3">
        <li>Choose the <strong>Security</strong> log by expanding <strong>Windows Logs</strong> in the Event Logs field.</li>
        <img width="539" alt="Screenshot 2024-12-01 at 11 35 32 PM" src="https://github.com/user-attachments/assets/f9620f90-3e5c-4afe-b0c1-faceb5c8e55e">
        <li>Scroll to the bottom of the filtered results and slowly move up. Look at the <strong>Task Category</strong> column for <strong>Security Group Management</strong>.</li>
    </ol>
    <p><strong>Note:</strong> Security Group Management events track changes to user group memberships, which directly influence system access and privileges.</p>
    <img width="1546" alt="Screenshot 2024-12-02 at 12 07 51 AM" src="https://github.com/user-attachments/assets/a085aed2-aaac-4150-a880-aa93a074a4cd">
    <p><strong>Answer:</strong> 03/02/2019 4:04:47 PM</p>
     <h2>13. What tool was used to get Windows passwords?</h2>
    <p>A command prompt kept popping up with the path <code>C:\TMP\mim.exe</code>, which provided a clue about the tool being used. Here are the steps to confirm this:</p>
    <ol>
        <li>Open <strong>File Explorer</strong>.</li>
        <li>Navigate to <code>Local Disk (C:) > TMP</code>.</li>
        <li>Inside the <code>TMP</code> directory, locate the file <code>mim.exe</code> and a related Word document named <code>mim-out</code>.</li>
        <li>Open <code>mim-out</code> to analyze its contents. It reveals the tool name.</li>
    </ol>
    <p><strong>Result:</strong></p>
    <img width="1127" alt="Screenshot 2024-12-03 at 4 47 53 PM" src="https://github.com/user-attachments/assets/8ef2f309-5755-4001-bc93-a848affffa60" />
    <img width="793" alt="Screenshot 2024-12-03 at 4 48 41 PM" src="https://github.com/user-attachments/assets/5e48eaed-e2ca-459a-9587-5eddfaa809c4" />
    <p><strong>Answer:</strong> Mimikatz</p>
     <h2>14. What was the attacker’s external control and command server’s IP?</h2>
    <p>To identify the attacker’s Command and Control (C2) server IP, follow these steps:</p>
    <ol>
        <li>Open <strong>File Explorer</strong>.</li>
        <li>Navigate to the file path: <code>C:\Windows\System32\drivers\etc</code>.</li>
        <li>Locate the file named <code>hosts</code> and open it using Notepad.</li>
    </ol>
    <p>The file contains entries that redirect domain names to specific IP addresses. As shown in the screenshot below, we can see entries for <code>google.com</code> and <code>www.google.com</code> pointing to a suspicious IP address.</p>
   <img width="795" alt="Screenshot 2024-12-12 at 11 52 49 PM" src="https://github.com/user-attachments/assets/ad2607c7-7ef3-440b-ad47-f0c9c8e1d58d" />
    <p>Using a tool like <a href="https://whois.domaintools.com/" target="_blank">Domain Tools</a>, we can check the IP address to confirm that it is not hosted by Google. Instead, it uses Google's domain to hide its true identity.</p>
    <img width="796" alt="Screenshot 2024-12-13 at 12 03 58 AM" src="https://github.com/user-attachments/assets/07fa9176-f9d5-440b-b866-75ab916b710f" />
    <p><strong>Answer:</strong> 76.32.97.132</p>
    <footer>
        <p><em>Created by [Your Name]</em></p>
    </footer>
</body>
</html>
