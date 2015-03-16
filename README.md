This module contains the following DSC resources to allow configuration of packages and PowerShell modules.

1) NugetPackage – allow you to download packages from the Nuget source location (e.g., http://nuget.org/api/v2/) and install/uninstall the package on your computer.

2) PSModule - download PowerShell modules from the PowerShell Gallery, "PSGallery"  (e.g., https://www.powershellgallery.com/api/v2/ ) and install it on your computer.

3) OneGetSource - Register/unregister a package source on your computer

**Installation**

To use the OneGetResource module,

• Copy the content under $env:ProgramFiles\WindowsPowerShell\Modules folder

To confirm installation,

• Run Get-DSCResource to see that NugetPackage, OneGetSource, PSModule are among the DSC Resources listed

**Requirements**:

This module requires the [February Preview of Windows Management Framework (WMF 5.0)](http://blogs.msdn.com/b/powershell/archive/2015/02/18/windows-management-framework-5-0-preview-february-2015-is-now-available.aspx).

**Examples**

This is an example for installing the Pester tool.

    configuration Sample_InstallPester
    {

      param
      (
        #Destination path for the package
        [Parameter(Mandatory)]
        [string]$DestinationPath
      )


     Import-DscResource -Module OneGetResource


     Node "localhost"
     {

        #register package source
        OneGetSource SourceRepository
        {

            Ensure      = "Present"
            Name        = "Mynuget"
            ProviderName= "Nuget"
            SourceUri   = "http://nuget.org/api/v2/"
            InstallationPolicy ="Trusted"
        }

        #Install a package from Nuget repository
        NugetPackage Nuget
        {
            Ensure          = "Present"
            Name            = "Pester"
            DestinationPath = $DestinationPath
            DependsOn       = "[OneGetSource]SourceRepository"
            InstallationPolicy="Trusted"
        }
     }
    }


Compile the configuration

Sample_InstallPester -DestinationPath "$env:HomeDrive\test"

Apply the configuration

Start-DscConfiguration -path .\Sample_InstallPester -wait -Verbose -force  
