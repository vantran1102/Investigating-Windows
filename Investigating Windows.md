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
    <footer>
        <p><em>Created by [Your Name]</em></p>
    </footer>
</body>
</html>
