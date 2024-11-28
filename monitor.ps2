# Configuration
$ServerIP = "0.0.0.0"         # IP or hostname of the server to monitor
$KnownGoodIP = "8.8.8.8"            # IP or hostname of a known good server (e.g., Google DNS)
$EmailTo = "to@example.com"
$EmailFrom = "from@example.com"
$Subject = "Server Down Alert"
$SMTPServer = "mail.example.com"
$SMTPPort = 587
$SMTPUsername = "username"
$SMTPPassword = "password"
$LogFile = "ServerMonitor.log"

# Create or clear log file
New-Item -Path $LogFile -ItemType File -Force | Out-Null
Add-Content -Path $LogFile -Value "Monitoring started at $(Get-Date)"

# Function to send email
function Send-AlertEmail {
    param (
        [string]$Body
    )
    $SMTPMessage = New-Object System.Net.Mail.MailMessage($EmailFrom, $EmailTo, $Subject, $Body)
    $SMTPClient = New-Object Net.Mail.SmtpClient($SMTPServer, $SMTPPort)
    $SMTPClient.EnableSsl = $true
    $SMTPClient.Credentials = New-Object System.Net.NetworkCredential($SMTPUsername, $SMTPPassword)
    $SMTPClient.Send($SMTPMessage)
    Add-Content -Path $LogFile -Value "[$(Get-Date)] Email sent: $Body"
}

# Monitoring loop
while ($true) {
    $ServerPing = Test-Connection -ComputerName $ServerIP -Count 1 -Quiet
    $GoodIPPing = Test-Connection -ComputerName $KnownGoodIP -Count 1 -Quiet

    if (-not $ServerPing -and $GoodIPPing) {
        $Message = "$ServerIP appears to be down!"
        Add-Content -Path $LogFile -Value "[$(Get-Date)] $Message"
        Send-AlertEmail -Body $Message
    }

    # Wait for a minute before the next check
    Start-Sleep -Seconds 60
}
