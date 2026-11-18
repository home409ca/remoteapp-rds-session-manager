# RemoteApp RDS Session Manager

A portable Windows utility for clearing Remote Desktop Services (RDS) sessions and reopening a RemoteApp. It provides a Polish-language interface and runs from a single executable.

## Features

- **Log off only** (*Tylko wyloguj*) — lists the specified user's sessions, asks for confirmation, logs off the approved sessions, and checks whether any remain.
- **Log off and open RemoteApp** (*Wyloguj i otwórz RemoteApp*) — displays the result and waits for you to click **OK** before opening the configured RemoteApp file. If the user was already signed out, it displays that result and still offers to open RemoteApp.
- **Open RemoteApp only** (*Otwórz tylko RemoteApp*) — opens the configured file without querying or changing RDS sessions.
- Supports `.rdp` files, Windows shortcuts (`.lnk`), and Internet shortcuts (`.url`).

## Quick Start

1. Run `RemoteApp-RDS.exe`. No installation or companion files are required.
2. Enter the RDS host's IPv4 address. The default is `192.168.3.1`.
3. Enter the target username. You can also specify the full account name as `DOMAIN\username`.
4. To use the RemoteApp launch options, click **Wybierz…** and select the file or shortcut you normally use.
5. Select an action. Before confirming a logoff, review the account, session IDs, and session states.

The RDS address controls where the utility queries and logs off sessions. RemoteApp connects using the settings in the selected file or shortcut. If you switch RDS hosts, select the corresponding RemoteApp file when needed.

## Session Handling

The utility uses Windows `quser` to list sessions and checks their account identities through the Windows RDS API. Username matching is exact and case-insensitive. If a username appears in multiple domains, enter `DOMAIN\username` to identify the intended account.

Before sending `logoff`, the utility rechecks each approved session. It skips sessions that have changed since confirmation and leaves newly discovered sessions untouched. Afterward, it queries the server again to verify the result.

**Save your work before confirming.** Logging off ends programs in both active and disconnected sessions. Unsaved work may be lost.

When Windows reports a launch error, the utility keeps its window open and displays the details. A successful local launch means Windows accepted the request to open the file. Sign-in and connection status are shown by the RemoteApp client.

## Requirements and Settings

- Windows with Windows PowerShell and Remote Desktop client components available.
- Network access to the RDS host, through the corporate network or VPN if required.
- RDS permissions to query sessions and log off the target account. The utility uses the Windows account under which it is running.
- A trusted RemoteApp file or shortcut if you want to use the launch options.

The utility remembers the RDS address, username, and RemoteApp path in `%APPDATA%\RemoteApp-RDS-192.168.3.1`. The username is blank on first use unless saved settings already exist. The utility does not ask for or store passwords.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| Access denied | Verify that your Windows account has permission to query and log off sessions on the RDS host. |
| The server cannot be reached | Check the RDS IPv4 address and your corporate network or VPN connection. |
| RemoteApp does not open | Try **Otwórz tylko RemoteApp** and review the error. Check whether the same file opens directly from File Explorer. |
| RemoteApp opens the wrong connection | Check the connection settings in the selected file or choose the shortcut for the intended environment. |
| Windows displays a warning | The executable is unsigned. Follow your organization's software approval process. |

## Implementation and Testing

The executable embeds the PowerShell WinForms interface and RDS logic in a single .NET Framework launcher. The packaged launcher and both workflow modes were checked using simulated sessions. Verify connectivity, permissions, and the configured RemoteApp shortcut in your own environment before relying on the utility.
