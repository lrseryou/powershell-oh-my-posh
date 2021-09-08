- 复制到powershell执行
```
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
choco feature enable -n allowGlobalConfirmation
choco install git
Install-Module posh-git
Install-Module oh-my-posh
Install-Module -Name PSReadLine -Force -SkipPublisherCheck
Install-Module Get-ChildItemColor -AllowClobber
Install-Module WindowsConsoleFonts
if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }
@"
#requires -Version 2 -Modules posh-git

function Write-Theme {
    param(
        [bool]
        `$lastCommandFailed,
        [string]
        `$with
    )

    `$lastColor = `$sl.Colors.PromptBackgroundColor
    `$prompt = Write-Prompt -Object `$sl.PromptSymbols.StartSymbol -ForegroundColor `$sl.Colors.PromptForegroundColor -BackgroundColor `$sl.Colors.SessionInfoBackgroundColor

    #check the last command state and indicate if failed
    If (`$lastCommandFailed) {
        `$prompt += Write-Prompt -Object "`$(`$sl.PromptSymbols.FailedCommandSymbol) " -ForegroundColor `$sl.Colors.CommandFailedIconForegroundColor -BackgroundColor `$sl.Colors.SessionInfoBackgroundColor
    }

    #check for elevated prompt
    If (Test-Administrator) {
        `$prompt += Write-Prompt -Object "`$(`$sl.PromptSymbols.ElevatedSymbol) " -ForegroundColor `$sl.Colors.AdminIconForegroundColor -BackgroundColor `$sl.Colors.SessionInfoBackgroundColor
    }

    `$user = [System.Environment]::UserName
    `$computer = [System.Environment]::MachineName
    `$path = Get-FullPath -dir `$pwd
    if (Test-NotDefaultUser(`$user)) {
        `$prompt += Write-Prompt -Object "`$user@`$computer " -ForegroundColor `$sl.Colors.SessionInfoForegroundColor -BackgroundColor `$sl.Colors.SessionInfoBackgroundColor
    }

    if (Test-VirtualEnv) {
        `$prompt += Write-Prompt -Object "`$(`$sl.PromptSymbols.SegmentForwardSymbol) " -ForegroundColor `$sl.Colors.SessionInfoBackgroundColor -BackgroundColor `$sl.Colors.VirtualEnvBackgroundColor
        `$prompt += Write-Prompt -Object "`$(`$sl.PromptSymbols.VirtualEnvSymbol) `$(Get-VirtualEnvName) " -ForegroundColor `$sl.Colors.VirtualEnvForegroundColor -BackgroundColor `$sl.Colors.VirtualEnvBackgroundColor
        `$prompt += Write-Prompt -Object "`$(`$sl.PromptSymbols.SegmentForwardSymbol) " -ForegroundColor `$sl.Colors.VirtualEnvBackgroundColor -BackgroundColor `$sl.Colors.PromptBackgroundColor
    }
    else {
        `$prompt += Write-Prompt -Object "`$(`$sl.PromptSymbols.SegmentForwardSymbol) " -ForegroundColor `$sl.Colors.SessionInfoBackgroundColor -BackgroundColor `$sl.Colors.PromptBackgroundColor
    }

    # Writes the drive portion
    `$prompt += Write-Prompt -Object "`$path " -ForegroundColor `$sl.Colors.PromptForegroundColor -BackgroundColor `$sl.Colors.PromptBackgroundColor

    `$status = Get-VCSStatus
    if (`$status) {
        `$themeInfo = Get-VcsInfo -status (`$status)
        `$lastColor = `$themeInfo.BackgroundColor
        `$prompt += Write-Prompt -Object `$(`$sl.PromptSymbols.SegmentForwardSymbol) -ForegroundColor `$sl.Colors.PromptBackgroundColor -BackgroundColor `$lastColor
        `$prompt += Write-Prompt -Object " `$(`$themeInfo.VcInfo) " -BackgroundColor `$lastColor -ForegroundColor `$sl.Colors.GitForegroundColor
    }

    # Writes the postfix to the prompt
    `$prompt += Write-Prompt -Object `$sl.PromptSymbols.SegmentForwardSymbol -ForegroundColor `$lastColor

    `$timeStamp = Get-Date -UFormat %r
    `$timestamp = "[`$timeStamp]"

    `$prompt += Set-CursorForRightBlockWrite -textLength (`$timestamp.Length + 4)
    `$prompt += Write-Prompt `$timeStamp -ForegroundColor `$sl.Colors.PromptForegroundColor

    `$prompt += Set-Newline

    if (`$with) {
        `$prompt += Write-Prompt -Object "`$(`$with.ToUpper()) " -BackgroundColor `$sl.Colors.WithBackgroundColor -ForegroundColor `$sl.Colors.WithForegroundColor
    }
    `$prompt += Write-Prompt -Object (`$sl.PromptSymbols.PromptIndicator) -ForegroundColor `$sl.Colors.PromptBackgroundColor
    `$prompt += ' '
    `$prompt
}

`$sl = `$global:ThemeSettings #local settings
`$sl.PromptSymbols.StartSymbol = ''
`$sl.PromptSymbols.PromptIndicator = [char]::ConvertFromUtf32(0x276F)
`$sl.PromptSymbols.SegmentForwardSymbol = [char]::ConvertFromUtf32(0xE0B0)
`$sl.Colors.PromptForegroundColor = [ConsoleColor]::White
`$sl.Colors.PromptSymbolColor = [ConsoleColor]::White
`$sl.Colors.PromptHighlightColor = [ConsoleColor]::DarkBlue
`$sl.Colors.GitForegroundColor = [ConsoleColor]::Black
`$sl.Colors.WithForegroundColor = [ConsoleColor]::DarkRed
`$sl.Colors.WithBackgroundColor = [ConsoleColor]::Magenta
`$sl.Colors.VirtualEnvBackgroundColor = [System.ConsoleColor]::Red
`$sl.Colors.VirtualEnvForegroundColor = [System.ConsoleColor]::White
"@>$env:userprofile"\Documents\WindowsPowerShell\Modules\oh-my-posh\2.0.230\Themes\Paradox.psm1"
@"
chcp 65001
Set-PSReadlineOption -EditMode Emacs
function which(`$name) { Get-Command `$name | Select-Object Definition }
function rmrf(`$item) { Remove-Item `$item -Recurse -Force }
function mkfile(`$file) { "" | Out-File `$file -Encoding ASCII }
Import-Module posh-git
Import-Module oh-my-posh
Import-Module Get-ChildItemColor
Import-Module WindowsConsoleFonts
Set-Alias l Get-ChildItemColor -option AllScope
Set-Alias ls Get-ChildItemColorFormatWide -option AllScope
Set-PoshPrompt -Theme aliens
"@ > $PROFILE
chcp 65001
Set-PSReadlineOption -EditMode Emacs
Import-Module posh-git
Import-Module oh-my-posh
Import-Module Get-ChildItemColor
Import-Module WindowsConsoleFonts
Set-Alias l Get-ChildItemColor -option AllScope
Set-Alias ls Get-ChildItemColorFormatWide -option AllScope
Set-PoshPrompt -Theme aliens
git clone https://github.com/powerline/fonts.git
cd .\fonts\
.\install.ps1
cd ..
del .\fonts\ -Recurse -Force
```

- 手动下载oh-my-posh
```
https://www.powershellgallery.com/api/v2/package/oh-my-posh
```
- 创建文件夹oh-my-posh，进入oh-my-posh文件夹，再创建文件夹3.180.0（根据oh-my-posh版本命名），下载好的.nupkg文件后缀名修改成.zip，解压到以下路径
```
C:\Program Files\WindowsPowerShell\Modules\oh-my-posh\3.180.0
```

- 重启powershell

- 获取主题列表
```
Get-PoshThemes
```

- 设置主题（临时）
```
Set-PoshPrompt aliens
```

- 更换主题，打开文件："C:\Users\用户名\Documents\WindowsPowerShell\Microsoft，PowerShell_profile.ps1",Set-PoshPrompt -Theme 后面的参数就是主题名
```
#chcp 65001
Set-PSReadlineOption -EditMode Emacs
function which($name) { Get-Command $name | Select-Object Definition }
function rmrf($item) { Remove-Item $item -Recurse -Force }
function mkfile($file) { "" | Out-File $file -Encoding ASCII }
#Import-Module posh-git
#Import-Module oh-my-posh
#Import-Module Get-ChildItemColor
#Import-Module WindowsConsoleFonts
Set-Alias l Get-ChildItemColor -option AllScope
Set-Alias ls Get-ChildItemColorFormatWide -option AllScope
#Set-PoshPrompt -Theme Paradox
Set-PoshPrompt -Theme aliens
```
