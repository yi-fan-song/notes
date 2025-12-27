---
tags: [.Net Core, .Net Framework, 'C#', Notebooks/.NET]
title: Creating a Window Service
created: '2019-11-11T02:40:26.250Z'
modified: '2019-11-11T02:52:09.093Z'
---

# Creating a Window Service

Window Services are background applications that can be launched on boot.
Accessing installed Window Services can be done through:
- Search for "Services" in the start menu
- Run (Window + R) "services.msc"

## Creating a window service using Visual Studio
Guide to create a window service using Visual Studio in C# .NET Framework/Core

#### Step 1:
Create a new project, select `Window Service`

#### Step 2:
Open and right click in the `{service}.[Design]` file and select `Add Installer`.

#### Step 3:
View installer code and open `InitializeComponent` definition.

#### Step 4:
Add this line:
```cs
this.serviceProcessInstaller1.Account = System.ServiceProcess.ServiceAccount.LocalSystem;
```
Optionally, for a project description and name add:
```cs
this.serviceInstaller1.Description = "My First Service demo";  

this.serviceInstaller1.DisplayName = "MyFirstService.Demo";
```

#### Step 5:
Write your service.

#### Step 6:
Build the service.

#### Step 7:
Launch cmd and admin and run `cd C:/Windows/Microsoft.NET/Framework/v4.0.30319`.
Alternatively, set and alias for `C:/Windows/Microsoft.NET/Framework/v4.0.30319/InstallUtil.exe`

#### Step 8:
Locate your install at `{project dir}/bin/debug/{service name}.exe`.
Then run `InstallUtil.exe {Installer Location}` (to uninstall add `-u` as a parameter to this command).

You can find all the installed services in the services browser.

[Source](https://dzone.com/articles/create-windows-services-in-c)
