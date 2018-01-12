# Dockerfile for Build Tools 2017 for compiling C/C++ Program + Fonts for Graphviz
# 
# VERSION:        0.2
#
# Windows:        Windows Server 10.0.14393.1770
# VS Build Tools: 15.5.x latest
#
# Use a specific tagged image. Tags can be changed, though that is unlikely for most images.
# You could also use the immutable tag @sha256:d841bd78721c74f9b88e2700f5f3c2d66b54cb855b8acb4ab2c627a76a46301d
FROM microsoft/windowsservercore:10.0.14393.1770

# Copy and install fonts which will used by Graphviz.
COPY Tools C:\\TEMP\\Tools

# Use PowerShell commands to download, validate hashes, etc.
SHELL ["powershell.exe", "-ExecutionPolicy", "Bypass", "-Command", "$ErrorActionPreference='Stop'; $ProgressPreference='SilentlyContinue'; $VerbosePreference = 'Continue';"]

# Download Build Tools 15.5.x latest and other useful tools.
ENV VS_BUILDTOOLS_URI=https://aka.ms/vs/15/release/vs_buildtools.exe \
    VS_BUILDTOOLS_SHA256=254B01CEDCF593F558C8038278243251DC8D43234AFA4626B34E6EBF13151106 \
    NUGET_URI=https://dist.nuget.org/win-x86-commandline/v4.4.1/nuget.exe \
    NUGET_SHA256=781930966CF218CA01560C11F99F3C7423AA734C3BBE480179B85A7EBA69D9B2

# Download tools to C:\Bin and install Build Tools excluding workloads and components with known issues.
RUN New-Item -Path C:\Bin -Type Directory | Out-Null; \
    [System.Environment]::SetEnvironmentVariable('PATH', "\"${env:PATH};C:\Bin\"", 'Machine'); \
    function Fetch ([string] $Uri, [string] $Path, [string] $Hash) { \
      Invoke-RestMethod -Uri $Uri -OutFile $Path; \
      if ($Hash -and ((Get-FileHash -Path $Path -Algorithm SHA256).Hash -ne $Hash)) { \
        throw "\"Download hash for '$Path' incorrect\""; \
      } \
    }; \
    Fetch -Uri $env:NUGET_URI -Path C:\Bin\nuget.exe -Hash $env:NUGET_SHA256; \
    Fetch -Uri $env:VS_BUILDTOOLS_URI -Path C:\TEMP\vs_buildtools.exe -Hash $env:VS_BUILDTOOLS_SHA256; \
    Fetch -Uri 'https://aka.ms/vscollect.exe' -Path C:\TEMP\vscollect.exe; \
    $p = Start-Process -Wait -PassThru -FilePath C:\TEMP\Tools\AddFonts.cmd -ArgumentList 'C:\TEMP\Tools\Fonts\ > NUL 2>&1'; \
    if (($ret = $p.ExitCode) -and ($ret -ne 0)) { throw ('Install fonts for Graphviz failed with exit code 0x{0:x}' -f $ret) } \
    $p = Start-Process -Wait -PassThru -FilePath C:\TEMP\vs_buildtools.exe -ArgumentList '--quiet --wait --norestart --nocache --installPath C:\BuildTools --add Microsoft.VisualStudio.Workload.NetCoreBuildTools --add Microsoft.VisualStudio.Workload.VCTools --add Microsoft.VisualStudio.Component.VC.Tools.x86.x64 --add Microsoft.VisualStudio.Component.VC.140 --add Microsoft.Component.VC.Runtime.UCRTSDK --add Microsoft.VisualStudio.Component.VC.ATLMFC --add Microsoft.Net.Component.4.6.1.SDK --add Microsoft.Net.Component.4.6.1.TargetingPack --add Microsoft.Net.Component.4.TargetingPack --add Microsoft.Net.ComponentGroup.TargetingPacks.Common --remove Microsoft.VisualStudio.Component.Windows10SDK.16299.Desktop --remove Microsoft.VisualStudio.Component.Windows10SDK.16299.UWP --remove Microsoft.VisualStudio.Component.Windows10SDK.16299.UWP.Native --remove Microsoft.VisualStudio.Component.Windows10SDK.10240 --remove Microsoft.VisualStudio.Component.Windows10SDK.10586 --remove Microsoft.VisualStudio.Component.Windows10SDK.14393 --remove Microsoft.VisualStudio.Component.Windows81SDK'; \
    if (($ret = $p.ExitCode) -and ($ret -ne 3010)) { C:\TEMP\vscollect.exe; throw ('Install failed with exit code 0x{0:x}' -f $ret) }

# Restore default shell for Windows containers.
SHELL ["cmd.exe", "/s", "/c"]

# Start developer command prompt with any other commands specified.
ENTRYPOINT C:\BuildTools\Common7\Tools\VsDevCmd.bat &&

# Default to PowerShell if no other command specified.
CMD ["powershell.exe", "-NoLogo", "-ExecutionPolicy", "Bypass"]