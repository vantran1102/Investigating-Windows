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
    <img src="images/system_info_full.png" alt="System Information Full">
    <p>To narrow the output to only properties starting with "OS," use:</p>
    <pre><code>Get-ComputerInfo -Property "OS*"</code></pre>
    <p><strong>Result:</strong></p>
    <img src="images/system_info_filtered.png" alt="Filtered System Information">
    <p><strong>Answer:</strong> Windows Server 2016</p>
    <h2>2. Which user logged in last?</h2>
    <p>To identify the last logged-in user, you can enumerate all local users or examine login events.</p>
    <h3>Option 1: Enumerate Local Users</h3>
    <pre><code>Get-LocalUser</code></pre>
    <p><strong>Result:</strong></p>
    <img src="images/local_users.png" alt="Local Users">
    <h3>Option 2: Check Event Viewer</h3>
    <ol>
        <li>Open Event Viewer with the PowerShell command:</li>
        <pre><code>eventvwr</code></pre>
        <li>Navigate to <strong>Windows Logs > Security</strong> and filter by Event ID <strong>4624</strong> (successful logons).</li>
    </ol>
    <p><strong>Result:</strong></p>
    <img src="images/event_viewer_login.png" alt="Event Viewer Logon">
    <p><strong>Answer:</strong> Administrator</p>
    <footer>
        <p><em>Created by [Your Name]</em></p>
    </footer>
</body>
</html>
