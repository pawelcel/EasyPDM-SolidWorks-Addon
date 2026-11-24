# EasyPDM add-in for SOLIDWORKS

An **EasyPDM** tab in the CommandManager (parts, assemblies, drawings), a **Tools → EasyPDM**
menu and the **EasyPDM document panel** in the Task Pane.

**Step by step, with screenshots of where everything is: [the wiki](https://github.com/pawelcel/EasyPDM-SolidWorks-Addon/wiki).**

![The EasyPDM tab](../docs/images/ribbon.png)

| Command | What it does |
|---|---|
| **Upload** | Runs the Upload macro (`EasyPDMUpload.swp`, `main`). Refused first when the document, or a component of the assembly, is older than its file in EasyPDM: download the newest, then upload. |
| **Download** | Runs the Download macro (`EasyPDMDownload.swp`, `main`). |
| **Lock** | Locks the active document's item for editing, or releases your lock: pressed while you hold the lock, greyed out for a released item or someone else's lock. |
| **Log in/out** | Logs in (the Upload macro's `LoginInBrowser`, approved in the browser) or out (`Logout`); pressed while you are signed in. |
| **Panel** | Opens the document panel. |

The **document panel** shows who is signed in and to which server, the active document's item
(record, name, revision, status, who holds the lock) with Refresh, Open in browser, Lock for
editing / Release lock, Upload and Log in / Log out, and for an assembly the tree of its
components, each marked as the newest file in EasyPDM, changed here, not in EasyPDM yet, or
with a newer file in EasyPDM (with who uploaded it and when, in its tooltip). It follows the
active document.

![The document panel for an assembly](../docs/images/panel-assembly-closeup.png)

The add-in uses the login and server address the macros keep in the registry, so signing in
once covers both; Upload and Download start the browser sign-in themselves when there is no
valid login. The outdated-component check needs an EasyPDM 0.9 server, and recognises a
file after SOLIDWORKS saves it again unchanged by the version stamp the Upload macro writes into
it (`EasyPDM_ContentVersion`); files uploaded by older macros are recognised by their content.

## Installing

1. Unpack `EasyPDM_SolidWorks_Addin_vX.zip` into a folder that stays (for example
   `C:\Program Files\EasyPDM\SolidWorks`). It holds the add-in, both compiled macros
   (`EasyPDMUpload.swp`, `EasyPDMDownload.swp`) and their `.bas` sources.
2. Right-click `install.cmd` → **Run as administrator**. It clears the "downloaded from the
   internet" mark and registers the add-in (`RegAsm /codebase`).
3. Start SOLIDWORKS. The add-in loads at start-up for the user who installed it; others tick
   **EasyPDM** under **Tools → Add-Ins**.

`uninstall.cmd` (as administrator) removes it.

## How it is built

A .NET Framework 4.8 class library (what SOLIDWORKS loads), COM-visible, implementing
`ISwAddin`. It compiles against the SOLIDWORKS 2024 SP1 interop assemblies from NuGet
(`SolidWorks.Interop.*` 32.1.0, signed by Dassault Systèmes SolidWorks Corp.) with their types
embedded, so the add-in ships none of them and binds to the installed SOLIDWORKS. It builds on
Linux too (`dotnet build -c Release`), which is how the release workflow builds it.

Texts are in English, Polish and German, in SOLIDWORKS' language (English when "Use English language menus"
is ticked), as in the macros.
Icons are drawn at start-up into `%LOCALAPPDATA%\EasyPDM\SolidWorks`. What the add-in asks
the server and decides is logged to `%TEMP%\EasyPDM_addin.log`, beside the macros' own logs.

## Status

Run on SOLIDWORKS 2024 SP5 (2026-11-17): the tab and menu, Upload, Download, Log in/out with
the browser sign-in, Lock, the document panel for parts and assemblies, and the language
following SOLIDWORKS. The refusal of an Upload built on an outdated component is built, not
yet seen on SOLIDWORKS.
