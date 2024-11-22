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
    <footer>
        <p><em>Created by [Your Name]</em></p>
    </footer>
</body>
</html>
