ARG REPO=mcr.microsoft.com/dotnet/aspnet
FROM $REPO:3.1.32-cbl-mariner1.0

ENV \
    # Unset ASPNETCORE_URLS from aspnet base image
    ASPNETCORE_URLS= \
    # Do not generate certificate
    DOTNET_GENERATE_ASPNET_CERTIFICATE=false \
    # Enable correct mode for dotnet watch (only mode supported in a container)
    DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip \
    # PowerShell telemetry for docker image usage
    POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-CBL-Mariner-1.0

# Install .NET Core SDK
RUN sdk_version=3.1.426 \
    dotnet_version=3.1.32 \
    aspnet_version=3.1.32 \
    && curl -fSL --output dotnet.rpm https://dotnetcli.azureedge.net/dotnet/Sdk/$sdk_version/dotnet-sdk-$sdk_version-x64.rpm \
    && dotnet_sha512='2190b9028cc1b4eff8ce292e57c4b1a4441a36ead2e9bc5e81363f77399ecc5165af638922712b6385e71665a787d679532a5b275a692fac55f8d2fdd2131bad' \
    && echo "$dotnet_sha512  dotnet.rpm" | sha512sum -c - \
    \
    && curl -fSL --output apphost.rpm https://dotnetcli.azureedge.net/dotnet/Runtime/$dotnet_version/dotnet-apphost-pack-$dotnet_version-x64.rpm \
    && dotnet_sha512='fe01d7354b71f56ed6ec387315d5292e88fd950b21a64ace2eacd054d56bbb949e9663483f06a4ebd8fab95d36bd7185ef11654e58135d3849830f52abe53744' \
    && echo "$dotnet_sha512  apphost.rpm" | sha512sum -c - \
    \
    && curl -fSL --output targeting-pack.rpm https://dotnetcli.azureedge.net/dotnet/Runtime/3.1.0/dotnet-targeting-pack-3.1.0-x64.rpm \
    && dotnet_sha512='9111ef4a0083c1fbd969f5adfca8f7b0a2e90eada94771c68ca4d00d34756fb280de153ecce4ad3da4b16698b7add4848f5b6ec24b89a144c2a119a42fb27c85' \
    && echo "$dotnet_sha512  targeting-pack.rpm" | sha512sum -c - \
    \
    && curl -fSL --output aspnetcore-targeting-pack.rpm https://dotnetcli.azureedge.net/dotnet/aspnetcore/Runtime/3.1.0/aspnetcore-targeting-pack-3.1.0.rpm \
    && dotnet_sha512='fdab7122896ff3602d8a25c0b283996973ef0cf4f4d0f9eba429d3a93b2441ebe0e0d0b499b97bb28c43cfb386501cc617ca7ed7c949dd3e45c61db0d5879fd4' \
    && echo "$dotnet_sha512  aspnetcore-targeting-pack.rpm" | sha512sum -c - \
    \
    && curl -fSL --output netstandard-targeting-pack.rpm https://dotnetcli.azureedge.net/dotnet/Runtime/3.1.0/netstandard-targeting-pack-2.1.0-x64.rpm \
    && dotnet_sha512='fab41a86b9182b276992795247868c093890c6b3d5739376374a302430229624944998e054de0ff99bddd9459fc9543636df1ebd5392db069ae953ac17ea2880' \
    && echo "$dotnet_sha512  netstandard-targeting-pack.rpm" | sha512sum -c - \
    \
    && rpm --install dotnet.rpm apphost.rpm targeting-pack.rpm aspnetcore-targeting-pack.rpm netstandard-targeting-pack.rpm \
    && rm dotnet.rpm apphost.rpm targeting-pack.rpm aspnetcore-targeting-pack.rpm netstandard-targeting-pack.rpm \
    # Trigger first run experience by running arbitrary cmd
    && dotnet help

# Install PowerShell global tool
RUN powershell_version=7.0.13 \
    && curl -fSL --output PowerShell.Linux.x64.$powershell_version.nupkg https://pwshtool.blob.core.windows.net/tool/$powershell_version/PowerShell.Linux.x64.$powershell_version.nupkg \
    && powershell_sha512='c667ff1765950b089349f60260ee8eb8b8e500236f4570d09c33dde02792b7599fc8b12b20e636c8788c3d15ed371cbf20a04ad5e3cb397351404e14ab471f2f' \
    && echo "$powershell_sha512  PowerShell.Linux.x64.$powershell_version.nupkg" | sha512sum -c - \
    && mkdir -p /usr/share/powershell \
    && dotnet tool install --add-source / --tool-path /usr/share/powershell --version $powershell_version PowerShell.Linux.x64 \
    && dotnet nuget locals all --clear \
    && rm PowerShell.Linux.x64.$powershell_version.nupkg \
    && ln -s /usr/share/powershell/pwsh /usr/bin/pwsh \
    && chmod 755 /usr/share/powershell/pwsh \
    # To reduce image size, remove the copy nupkg that nuget keeps.
    && find /usr/share/powershell -print | grep -i '.*[.]nupkg$' | xargs rm
